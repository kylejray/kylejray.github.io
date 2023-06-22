---
layout: post
title:  "Using mpi4py to make embarassingly parallel computation simple"
date:   2022-08-20 01:35:00 -0800
categories: parallel computing, MPI, python
---

### What are we doing here?

I have been using our computing cluster for a couple years now to do large suites of simulations that would otherwise take weeks to run on my laptop. Since I work mostly in simulations of nanoscale thermal systems, I often am looking for good satistics on noisy and uninterating systems-- so this can be all I need.

It was a tiny bit intimidating at firs, but pretty quickly I realized that these kind of tasts are massively easy to run and incorporate into python, using the mpi4py library.

The instalation of mpi4py can be a little finicky in the way it interacts with your MPI distribution (which also will need to be installed). I usually make sure that when I run MPI code, I am using a clean environment that has only what is necesarry to run the code. But this isnt about the installation, it's supposed to be a quick and dirty guide to get a bunch of jobs running simultaneously when they dont need to communicate.

### Boilerplate MPI code
Basically all my 'parallel' computaing code follows the same basic outline. I start by importing MPI, and always run these lines at the beginning of the script:

{% highlight python %}
from mpi4py import MPI
comm = MPI.COMM_WORLD
size, rank = comm.Get_size(), comm.Get_rank()
{% endhighlight %}
This will define the most important variable in the whole setup: "rank". This variable will give you the current process ID and will be the basis for assinging dfferent tasks to different procs. And, thats what we do next.

Assume we have some class "SimRunner" in a module "simpack" that has a method "run", which does everyting we want it to do, provided it is initialized with some argument beween 0 and 1. All we need to do is use the variable "rank" to assing different arguments to different processes:

{% highlight python %}
from simpack import SimRunner
import numpy as np
# here we divide up the range of parameters we want to input
simParams = np.linspace(0, 1, size)

localSim = SimRunner()

localSim.initialize(simParams[rank])

{% endhighlight %}

The final step is to run the simulation and save the output however you please (I usually use a method called "save"). I often like to put some print statements in so I can check the progress.

{% highlight python %}
import sys

print(f'rank {rank} starting sim with p={simParam[rank]}')
#MPI like to cache all the print statements and then dump them all at the end. 
#One way to make sure they print out during
#the process is to consistently flush this cache using sys.stdout.flush()
sys.stdout.flush()
localSim.run()
#I also put this after the .run() method 
#if it has a verbose output I want to see in real time.
sys.stdout.flush()
localSim.save()

{% endhighlight %}

### Running the MPI script
After writing the simple script above and saving it to a text file (let's say we call it 'shameful_mpi.py'), running it is the simplest thing in the world. First make sure you are in the correct conda environment, and then run:

{% highlight bash %}
mpirun -n <N> python shameful_mpi.py
{% endhighlight %}

The "N" argument just tells you how many processes to spawn. Thats it! We are done. 

### Simple extension if you are node limited
Lets say you only have 8 nodes to work on, but you want to run 30 parameters. This can be dealth with simply by breaking up your parameters into subsets of 4 and then running serially through one subset on each process. For example:

{% highlight python %}

simParams = np.linspace(0,1,30)

#first determine how many element we need in each subset:
import math
L = math.ceil(len(SimParams)/size)

paramLists = [ simParams[i*L:(i+1)*L] for i in range(size) ]

localSim = SimRunner()

#assing a subset to each process
SimParams = paramLists[rank]

#and then assing the elements in the subset with a serial loop
for param in SimParams:
    localSim.initialize(param)
    #continue as beofore within the loop

{% endhighlight %}












