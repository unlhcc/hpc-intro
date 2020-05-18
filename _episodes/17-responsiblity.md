---
title: "Using resources effectively"
teaching: 10
exercises: 5
questions:
- "How can I be a responsible cluster user?"
objectives:
- "Be a good person and be nice to other users."
keypoints:
- "Don't run stuff on the login node."
- "Again, don't run stuff on the login node."
- "Don't be a bad person and run stuff on the login node."
---

You now have everything you need to run jobs, transfer files, use/install software, and monitor how
many resources your jobs are using.

So here are a couple final words to live by:

## Be kind to the login node

* The login node is very busy managing lots and lots of jobs! It doesn’t have any extra space to 
  run computational work. Don’t run jobs on the login node, though quick tasks like downloading 
  a file or compiling a small program are generally fine. You'll often find limits set on the 
  login/head node that prevent you from using too many resources.  Attempting to run programs 
  when resource limits are set will often cause your programs to die with strange errors. 
  Remember, the login node is to be shared with other users.  

* Most clusters have a queue available for interactive use called 
  {% for queue in site.workshop_interactive_queues %}{{ queue }}, {% endfor %} etc.  
  This is generally a better way to run small tests or compile programs. Try `sinfo` to see what
  queues (partitions) are available:

    ```
    [remote]$ sinfo
    ```
    {: .bash}
    ```
    batch*          up 7-00:00:00    246    mix c[0120,0122...1316]
    devel           up 7-00:00:00      4   idle c[1723-1726]
    guest_gpu       up 7-00:00:00      4  alloc c[0610,0612,0614,0616]
    guest_gpu       up 7-00:00:00      4   idle c[2619,2621,2623,2625]
    ....
    ```
    {: .output}

> ## Login Node Etiquette
> 
> Which of these commands would probably be okay to run on the login node?
> 1. python physics_sim.py
> 2. make
> 3. create_directories.sh
> 4. molecular_dynamics_2
> 5. tar -xzf R-3.3.0.tar.gz
{: .challenge}

* If someone is being inappropriate and using the login node to run all of their stuff, message an
  administrator to take a look at things and deal with them.

## Test before scaling

* Before submitting a large run of jobs, submit one as a test first to make sure everything works.

## Have a backup plan

* Use a Version Control system like git to keep track of your code. Though most systems have some
  form of backup/archival system, you shouldn't rely on it for something as key as your research
  code. The best backup system is one you manage yourself.

* Eventually, your data will need to leave the cluster. You should have a plan of where you’ll 
  store all your results *before* you run jobs.

## Save time

* Compress files before transferring to save file transfer times with large datasets.

* The less resources you ask for, the faster your jobs will find a slot in which to run. Lots of
  small jobs generally beat a couple big jobs.

## Software tips

* You can generally install software yourself, but if you want a shared installation of some kind,
  it might be a good idea to message an administrator.

* Always use the default compilers if possible. Newer compilers are great, but older stuff 
  generally has less compatibility issues.

## What to do if you're stuck
 * [Read the Documentation](hcc.unl.edu/docs)
   * If the documentation doesn’t answer your question,let us know!
 * Use `man` or `--help`
 * Consult Google
 * Contact Us
   * Drop in our offices
     * UNL: Schorr Center –Room 118
     * UNO: Peter Kiewit Institute –Room 152
   * Email [hcc-support@unl.edu](mailto:hcc-support@unl.edu)

{% include links.md %}
