---
layout: post
title:  "using mpi4py for actually parallel computing: take 2"
date:   2023-08-11
categories: parallel computing, MPI, python, HPC, high performance computing
---
{% include mathjax.html %}

#### note: part 2 of a series on using MPI in python, I suggest reading [the previous one before continuing](https://kylejray.github.io/parallel/computing,/mpi,/python,/hpc,/high/performance/computing/2023/06/21/unashamedly_parallel.html)

### Refresher

In the second part of this series, we tried to do some very simple parallel computing (without being "embarassingly parallel") using MPI. The task was simple: have several independent processes(procs) guess random integers from 1 to 10, scoring points for integers havent been guessed yet. We achieved somethig kind of close to this, but we weren't able to de-sync the procs. Basically, even by making some procs slower tha others-- each was still getting the same number of guesses over the full computation. An obvious reason for this is that the previous treatment was symmetric with repoect to the procs. Each was treated as the same as all the others and, once per loop, we used comm.allreduce to sync up the lists.

So, let's try something different. We will re-write the code so that proc 0 is special. It will not be guessing, but just servng as a hub for the other processes to send information to. We will assign proc 0 to be this middle man. Here is some updated code, based on where we left off:

{% highlight python %}
import numpy as np
import sys
from time import sleep
from time import time

from mpi4py import MPI
comm = MPI.COMM_WORLD
size = comm.Get_size()  # number of MPI procs
rank = comm.Get_rank()  # i.d. for local proc

def rel_time(start_time):
    current_time = time()
    return (current_time - start_time)


stime = None
final_list = None
trial_n = 0
new_vals = [0]

# make sure that the clock starts the same for all procs
if rank==0:
    stime=time()
    final_list = [0]
stime = comm.bcast(stime, root=0)

def tprint(string):
    print(f'{rel_time(stime): 3.0f}'+' '+string)
    return

i = 0
j = 0


while len(comm.bcast(final_list,root=0)) < 10:
    i += 1
    sleep(rank+.1)

    # only make a new guess for procs that arent proc 0
    if rank != 0:
        trial_n = np.random.randint(10)
        tprint(f'rank {rank} guess {i}: {trial_n}; has {j} points \r ')
        sys.stdout.flush()

    # here we check against the list held in proc 0, rather than trying to sync the liust between all procs
    if trial_n not in comm.bcast(final_list, root=0):
        j += 1

        #send the guesses to the zero proc only when a new value is found that hasn't been guessed, and only to the zero proc
        comm.send(trial_n, dest=0)

    # now, proc 0 (and only proc 0) will recieve any of the new values
    if rank==0:
        new_vals = []
        # we loop over all the procs that are not proc 0, recieve any incoming integers and then add them to the new_vals
        for i in range(size-1):

            trial_n = comm.recv(source=i+1)
            tprint(f'rank {rank} recieved {trial_n}')
            sys.stdout.flush()
            new_vals.append(trial_n)

        IFS = final_list
        final_list =list(set((final_list + new_vals)))

        if IFS != final_list:
            tprint(f'new list {final_list}')
            sys.stdout.flush()

comm.barrier()
if rank !=0:
    tprint(f'rank {rank} got {j} points with {i} guesses')

{% endhighlight %} 

Does this approach seem plausible to you? Take some time to make sure you understand what we are trying to do. Go ahead and try to run it with just 3 processes (it isn't going to work, but try it anyway fr the experience.)

### Why did we fail again?

You should have found that the program will hang unless every guessng procedure sends a message. The reason? MPI really really really needs to have a one-to-one correspondence with messages sent and messages recieved. So, when it comes to a line like comm.recv(source=1) and proc 1 hasnt sent a message, it is just going to hang there until it gets a message. But, this also stops the loop from progessing, and so it totally halts all the other processes. Thus, no message will ever come. The most straightforward soluton to this is to have a boolean flag for each procesure that proc 0 can look at to see if there is or is not a message incoming. Then, we can make it only comm.recv on procs that have messages to be recieved. See below for the inclusion of this boolean flag.


{% highlight python %}
# the above is unchanged
while len(comm.bcast(final_list,root=0)) < 10:
    #initialize all procs to not be sending any messages
    message=False
    i += 1
    sleep(2*rank+.1)

    if rank != 0:
        trial_n = np.random.randint(10)
        tprint(f'rank {rank} guess {i}: {trial_n}; has {j} points \r ')
        sys.stdout.flush()

    if trial_n not in comm.bcast(final_list, root=0):
        j += 1
        # when we send the message, we also change boolean message flag to "True", not we have to give comm.send a identifying tag because now we have multiple types of messages being sent around
        message=True
        comm.send(trial_n, dest=0, tag=0)

    # we need to send the boolean message to proc 0 EVERY TIME or else proc 0 will hang when looking for it
    if rank != 0:
        comm.send(message, dest=0, tag=1)
    
    if rank==0:
        #tprint(f'messages {messages}')
        #sys.stdout.flush()
        new_vals = []
        for i in range(size-1):
            # we enter the lop of receiving a guess ONLY if the inital check of "is there a real messge waiting" is achieved
            if comm.recv(source = i+1, tag=1):
                trial_n = comm.recv(source=i+1, tag=0)
                tprint(f'rank {rank} recieved {trial_n}')
                sys.stdout.flush()
                new_vals.append(trial_n)
            else:
                pass
# the rest is unchanged
{% endhighlight %} 

Ok, now this is starting to look more like it! Give it a go. Can you get it to run? (this time, it should)

### Did we fail yet again?

Unfortunately, it looks like we did! Here is a sample of a run of this code:
{% highlight bash %}
% mpirun -n 3 python parallel_blocking.py

  1 rank 1 guess 1: 3; has 0 points 
  1 rank 0 recieved 3
  2 rank 2 guess 1: 2; has 0 points 
  2 rank 0 recieved 2
  2 new list [0, 2, 3]
  3 rank 1 guess 2: 1; has 1 points 
  3 rank 0 recieved 1
  4 rank 2 guess 2: 4; has 1 points 
  4 rank 0 recieved 4
  4 new list [0, 1, 2, 3, 4]
  5 rank 1 guess 3: 8; has 2 points 
  5 rank 0 recieved 8
  6 rank 2 guess 3: 6; has 2 points 
  6 rank 0 recieved 6
  6 new list [0, 1, 2, 3, 4, 6, 8]
  7 rank 1 guess 4: 1; has 3 points 
  8 rank 2 guess 4: 3; has 3 points 
 10 rank 1 guess 5: 3; has 3 points 
 11 rank 2 guess 5: 4; has 3 points 
 12 rank 1 guess 6: 0; has 3 points 
 13 rank 2 guess 6: 8; has 3 points 
 14 rank 1 guess 7: 1; has 3 points 
 15 rank 2 guess 7: 7; has 3 points 
 15 rank 0 recieved 7
 15 new list [0, 1, 2, 3, 4, 6, 7, 8]
 16 rank 1 guess 8: 6; has 3 points 
 17 rank 2 guess 8: 2; has 4 points 
 18 rank 1 guess 9: 3; has 3 points 
 19 rank 2 guess 9: 8; has 4 points 
 20 rank 1 guess 10: 4; has 3 points 
 21 rank 2 guess 10: 3; has 4 points 
 22 rank 1 guess 11: 4; has 3 points 
 23 rank 2 guess 11: 9; has 4 points 
 23 rank 0 recieved 9
 23 new list [0, 1, 2, 3, 4, 6, 7, 8, 9]
 24 rank 1 guess 12: 3; has 3 points 
 25 rank 2 guess 12: 1; has 5 points 
 26 rank 1 guess 13: 5; has 3 points 
 26 rank 0 recieved 5
 27 rank 2 guess 13: 6; has 5 points 
 27 new list [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
 27 rank 1 got 4 points with 13 guesses
 27 rank 2 got 5 points with 13 guesses
{% endhighlight %}

They are still getting the same number of guesses, when one of the processes should be able to guess about two times faster than the other! The issue is that despite conceptually disentangling proc 0 from the other procs, the message passing that we are usng is fundamentally what is called "blocking" communication. This means that, even on the very first loop, proc 0 cannot continue until it gets a message from every other proc. We have avoided the error by using the "message" boolean-- but we have kicked the parallelization can down the road because it still synchronizes the lops. The loop cannot go on to the next iteration until proc0 receves that boolean message from each procedure.

Another issue is the comm.bcast, which also requies that all the different processes "catch up" before beng able to continue. This could be worked around as well, using more explicit comm.send and comm.recv calls; but again, it wont solve the fundamental issue.

The solution is to use explicitly "nonblocking" communication instead. This is a set of MPI bindings that are structured to not hold up other processes. I think we have finally pinpointed the issue, so my next post will definitely be about how we can finally play the unfair guessing game we deserve!


