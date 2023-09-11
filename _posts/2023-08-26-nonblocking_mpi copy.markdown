---
layout: post
title:  "nonblocking MPI message passing in python"
date:   2023-08-26
categories: parallel computing, MPI, python, HPC, high performance computing
---
{% include mathjax.html %}

#### note: part 3 of a series on using MPI in python, I suggest reading [the previous one before continuing](https://kylejray.github.io/parallel/computing,/mpi,/python,/hpc,/high/performance/computing/2023/08/11/unashamedly_parallel_2.html)



### Refresher
In the third part of this series, we continued to try to do some very simple parallel computing (without being "embarassingly parallel") using MPI. The task was simple: have several independent processes(procs) guess random integers from 1 to 10, scoring points for integers havent been guessed yet. We achieved somethig kind of close to this, but we weren't able to de-sync the procs. Basically, even by making some procs slower tha others-- each was still getting the same number of guesses over the full computation. The solution was to step away from the usual results you get when you google how to use MPI: allreduce, gather, broadcast, etc.... Instead, we are going to use nonblockng MPI message passing. 


### Explicitly nonblocking communication in MPI
Primarily, our savior is comm.Iprobe(source=i), which will return a boolean depending on wether the given source has incoming messages for the proc or not. This is a built in way to implement the workaround that we tried previously ( recall our "message" boolean) but in a nonblocking way. It will not hang if there isnt a message yet, it will smply return a False and keep going. With this tool, we can manually control the messages that flow in and out of proc 0 in a way that allows all procs to go at their own pace. Let's use comm.Iprobe to retool our code:

{% highlight python %}
# the beginning is the same, but well need to initialze some extra variables
final_list = []
timeout = 100

# the break condition will come at the end, rather than at the beginning. here we add a timeout in case of unexpected failures
while i < timeout:
    i += 1
    sleep(rank+.1)
    
    if rank != 0:
        # use comm.Iprobe to check if there is a new list incoming from proc 0
        # if there is, we update our local list, we need a local list because
        # broadcastig from proc 0 is blocking communication
        if comm.Iprobe(source=0, tag=1):
            final_list = comm.recv(source=0, tag=1)

        tprint(f'rank {rank} guess {i} current list:{final_list}')
        trial_n = np.random.randint(10)
        tprint(f'rank {rank} guess {i}: {trial_n}; has {j} points \r ')
        sys.stdout.flush()

        if trial_n not in final_list:
            j += 1
            # note we dont need the message boolean anymore
            comm.send(trial_n, dest=0, tag=0)


    if rank==0:
        new_vals = []
        for i in range(size-1):
            # this will check if there is a new number pending on any proc and then add it to the master list (kept on proc 0)
            if comm.Iprobe(source=i+1):
                j += 1
                trial_n = comm.recv(source=i+1, tag=0)
                final_list.append(trial_n)
                final_list = list(set(final_list))
                
                tprint(f'added {trial_n} to list, list {j} is {final_list}')
                sys.stdout.flush()

                # every time we add a number to the list, we sed it out to all other procs
                for i in range(size-1):
                    comm.send(final_list, dest=i+1, tag=1)
    # once each proc recieves a totally full list, it knows that the game is over
    if len(final_list) > 9:
        break

# this barrier means no proc prints its score until all the procs are done. 
comm.barrier()
if rank !=0:
    tprint(f'rank {rank} got {j} points with {i} guesses')

{% endhighlight %}

### One more catch!

Ok, so the script above still isn't quite going to cut it. If you run it you will find that, while the loops can now happen independently, the local lists do not stay up to date with the newest iteration. This can be fixed by changing one word only. Before looking at the solution, maybe take some time to analyze what you think is happening. Run the code a few times and pay attention to what is printed...

Now, that you have done that, here is the answer. All you need to do is change

{% highlight python %}
if comm.Iprobe(source=0, tag=1):
    final_list = comm.recv(source=0, tag=1)
{% endhighlight %}
to 
{% highlight python %}
while comm.Iprobe(source=0, tag=1):
    final_list = comm.recv(source=0, tag=1)
{% endhighlight %}

You see, the issue is that the list update messages from proc 0 are queued up, and if you just check if there is a message or not and then update the list one time you will only get the oldesr message in the queue. The solution is, then, to completely clear out the queue each time you get to it. A while look does this perfectly, because it will keep returnig true until there are no more messages in the queue. This means you have recieved the most recent list and are good to go!
