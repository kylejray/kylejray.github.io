---
layout: post
title:  "Using mpi4py for actually parallel computing: take 1"
date:   2023-06-21 01:35:00 -0800
categories: parallel computing, MPI, python, HPC, high performance computing
---

### unashamedly parallel computing

For several years, I have been satisfied by running parallel processes that don't need to talk to eachother. Mostly, I run large sims to get good statistics, and so this works totally fine. However, some of my more recent ideas to speed up some search algoithms will involve real comunication. In service of this, I am finally giving up my "embarassing only" policy when it comes to multi-node computing.

### The goal
In order to try this out, I have set up what I think is a very simple task that requires the processes to talk to eachother (It also captures the core action of what I want to use it for, but that's just gravy).

The code we are going to work on is simple: a guessing game. Each process will be given the task of guessing digits that havent been guessed by any of the other processes yet. They will go on guessing until all the 1-9 positive integers have been guessed.

While the goal is easy enough, actually making it happen is another matter. Take a look at the minimal working example below. Despite the pleothera of example MPI scripts online, I promise you that they wont just do what you want them to do out of the box. The code below actualy comes at the expense of a lot of trial and error--despite looking very simple.


{% highlight python %}
from mpi4py import MPI
comm = MPI.COMM_WORLD
size, rank = comm.Get_size(), comm.Get_rank()

import numpy as np
import sys



final_list = [0]

i = 0
j = 0


while len(final_list) < 10:
    i += 1
    trial_n = np.random.randint(10)
    
    print(f'rank {rank} guessed {trial_n} on guess {i}; has {j} points')
    sys.stdout.flush()

    #this is the comminication part, it syncs the list between processes
    final_list = list(set(comm.allreduce(final_list)))

    if trial_n not in final_list:
        j += 1

        final_list = final_list + [trial_n]

        #this is the comminication part, it syncs the list between processes
        final_list = list(set(comm.allreduce(final_list)))

        print(f'rank{rank} guess{i} scores!')
        sys.stdout.flush()

print(f'rank {rank} got {j} points with {i} guesses')

{% endhighlight %}

## stop here, and actually run the code above before proceeding. 

It really is important to play around with this stuff first hand. There is no substitute for experience. Run it with 2 processes and with 5, and whatever else you might think of. Run it multiple times. Try to figure out if its working as intended. I looked at a dozen or so MPI templates and tutorials before I started trying to use it, and essentally none of the knwledge I "gained" doing this was worth anything. If you dont know how to run a piece of python code that uses MPI, you can look at my [post](https://kylejray.github.io/parallel/computing,/mpi,/python/2022/08/20/embarassing-parallel.html) about embarassingly parallel computation using mpi4py.

### did it work?

The code does approximately what we were hopinh, but there is more going on under the hood. First off, if there is a "tie" in that two processes guess the same number o the same guess-- they both get a point. We didn't specify this rule, so its not exactly a failure, but we do want to know how to aovid this. There is some implicit loop-syncing going on between the processes. After running the code with a few different options, I noticed that the number of guesses plus the number of points is the same for every process, implying the computation in one process is blocked at certain point, until the other processes "catch up". Let's verify this by implementing a time.sleep(rank+1) command before each guess. Naively, this should cause high rank processes to be slower and get few (if any) guesses in before the game ends.

We are going use datime as well, which is useful for figuring out when things happen. By now, you may have noticed that the order of the print statements that come from an MPI script is a bit unintuitive. Here is a helpful tool I use a lot when debugging and timing things:

{% highlight python %}
import datetime
def rel_time(start_time):
    current_time = datetime.datetime.now*()
    return (current_time-start_time).total_seconds()
{% endhighlight %}

Now, let's ammend the script above-- to include our changes. This will also give us a chance to play around with the comm.bcast MPI command; whihc allows us to boradcast the initial starting time from one process to all the others. This ensures they have a common reference point.

{% highlight python %}
from mpi4py import MPI
comm = MPI.COMM_WORLD
size, rank = comm.Get_size(), comm.Get_rank()

import numpy as np
import sys
import datetime
from time import sleep

#define global variable on all ranks initialized to None, this is necesarry for MPI to work properly
stime = None

#generate a value only on rank 0
if rank==0:
    stime=datetime.datetime.now()

#broadcast that value to all ranks, so they have a common reference time
stime = comm.bcast(stime, root=0)

#define the rel_time function
def rtime():
    return rel_time(stime)

#and a new print function that will add the relative time to the front
def tprint(string):
    print(f'{rtime():.2f}: ' + string )
    return

final_list = [0]
i = 0
j = 0

while len(final_list) < 10:
    i += 1
    #each process waits before generating its guess, higher numbers take longer
    sleep(rank+1)
    trial_n = np.random.randint(10)
    
    #note the change from "print" to "tprint"
    tprint(f'rank {rank} guessed {trial_n} on guess {i}; has {j} points')
    sys.stdout.flush()

    final_list = list(set(comm.allreduce(final_list)))
    # added a new tprint here, to keep track of wether allreduce is causing the syncng issue
    tprint(f'rank {rank} guess {i} init_list: {final_list}')
    sys.stdout.flush()

    if trial_n not in final_list:
        j += 1

        final_list = final_list + [trial_n]

        final_list = list(set(comm.allreduce(final_list)))

        tprint(f'rank{rank} guess{i} scores!')
        sys.stdout.flush()

tprint(f'rank {rank} got {j} points with {i} guesses')

{% endhighlight %}

## again, run the code before proceeding

### unanswered questions

After running this verson of the code, we can see some really interesting behavior in the timing. Here is a snippet of a simple 2 process game:

{% highlight bash %}
 .00: rank 0 guessed 1 on guess 1; has 0 points 
 .01: rank 1 guessed 1 on guess 1; has 0 points 
2.01: rank 0 guess 1 init_list: [0]
2.01: rank 1 guess 1 init_list: [0]
2.01: rank 0 guess 1 added:[1]
2.01: rank 1 guess 1 added:[1]
 .02: rank 0 guessed 5 on guess 2; has 1 points 
 .02: rank 1 guessed 3 on guess 2; has 1 points 
4.02: rank 0 guess 2 init_list: [0, 1]
4.02: rank 1 guess 2 init_list: [0, 1]
4.02: rank 0 guess 2 added:[3, 5]
4.02: rank 1 guess 2 added:[3, 5]
 .02: rank 0 guessed 7 on guess 3; has 2 points 
 .02: rank 1 guessed 0 on guess 3; has 2 points 
6.02: rank 0 guess 3 init_list: [0, 1, 3, 5]
6.02: rank 1 guess 3 init_list: [0, 1, 3, 5]
 .02: rank 1 guessed 4 on guess 4; has 2 points 
8.02: rank 0 guess 3 added:[7]
8.02: rank 1 guess 4 init_list: [0, 1, 3, 5, 7]
 .02: rank 0 guessed 5 on guess 4; has 3 points 
9.02: rank 0 guess 4 init_list: [0, 1, 3, 4, 5, 7]
9.02: rank 1 guess 4 added:[4]
 0.02: rank 0 guessed 7 on guess 5; has 3 points 
 1.03: rank 1 guessed 7 on guess 5; has 3 points 
11.03: rank 0 guess 5 init_list: [0, 1, 3, 4, 5, 7]
11.03: rank 1 guess 5 init_list: [0, 1, 3, 4, 5, 7]
 2.03: rank 0 guessed 0 on guess 6; has 3 points 
 3.03: rank 1 guessed 8 on guess 6; has 3 points 
13.03: rank 0 guess 6 init_list: [0, 1, 3, 4, 5, 7]
13.03: rank 1 guess 6 init_list: [0, 1, 3, 4, 5, 7]
 4.03: rank 0 guessed 6 on guess 7; has 3 points 
14.03: rank 0 guess 7 init_list: [0, 1, 3, 4, 5, 7, 8]
14.03: rank 1 guess 6 added:[8]
 6.04: rank 1 guessed 9 on guess 7; has 4 points 
16.04: rank 0 guess 7 added:[6]
16.04: rank 1 guess 7 init_list: [0, 1, 3, 4, 5, 6, 7, 8]
 7.04: rank 0 guessed 0 on guess 8; has 4 points 
17.04: rank 0 guess 8 init_list: [0, 1, 3, 4, 5, 6, 7, 8, 9]
17.04: rank 1 guess 7 added:[9]
 8.04: rank 0 guessed 4 on guess 9; has 4 points 
 9.04: rank 1 guessed 1 on guess 8; has 5 points 
{% endhighlight %}


 My initial guess is that it is the allreduce operations that cause the syncing issues, but it is hard to tell, because the guessing is not delayed in the way I would have expected. Sometime, the relative delay of 1 second seems to work but other times it does not. But these times do not look so reliable. Further testing is definitely necesarry.
 
 At first glance, it seems almost like allreduce cannot happen until all of the processes reach an allreduce line. In order to really make this work, we might need to deal with some manual sending/recieving from/to specific processes instead of relying on allreduce. This post is already geting too long, the next post will continue this investigation.










