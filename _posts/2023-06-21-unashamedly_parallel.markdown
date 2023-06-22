---
layout: post
title:  "Using mpi4py for actual parallel computing"
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

### unanswered questions

While this does do the task we assinged, there is more going on under the hood. For example, I have noticed that there is some implicit loop-syncing going on between the processes. Meaning that computation in one process is blocked at certain point, untol the other process "catches up". A really good way to see this is to implement a time.sleep(5*rank+1)) command before each guess. This will cause high rank processes to be slower. You would think this allows the low ranks to get in many more guesses... but oyu would be wrong. What is going on here?












