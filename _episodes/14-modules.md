---
title: "Accessing software"
teaching: 30
exercises: 15
questions:
- "How do we load and unload software packages?"
objectives:
- "Understand how to load and use a software package."
keypoints:
- "Load software with `module load softwareName`"
- "Unload software with `module purge`"
- "The module system handles software versioning and package conflicts for you automatically."
- "You can edit your `.bashrc` file to automatically load a software package."
---

On a high-performance computing system, no software is loaded by default. If we want to use a
software package, we will need to "load" it ourselves.

Before we start using individual software packages, however, we should understand the reasoning
behind this approach. The two biggest factors are software incompatibilities and versioning.

Software incompatibility is a major headache for programmers. Sometimes the presence (or absence) 
of a software package will break others that depend on it. Two of the most famous examples are 
Python 2 and 3 and C compiler versions. Python 3 famously provides a `python` command that 
conflicts with that provided by Python 2. Software compiled against a newer version of the C 
libraries and then used when they are not present will result in a nasty `'GLIBCXX_3.4.20' not
found` error, for instance.

Software versioning is another common issue. A team might depend on a certain package version for
their research project - if the software version was to change (for instance, if a package was
updated), it might affect their results. Having access to multiple software versions allow a set of
researchers to prevent software versioning issues from affecting their results.

## Environment modules (Lmod)

Environment modules are the solution to these problems. A module is a self-contained software
package - it contains all of the files required to run a software package and loads required
dependencies.

To see available software modules, use `module avail`

```
[remote]$ module avail
```
{: .bash}
```
----------------------------- MPI-dependent avx2 modules -------------------------------
abinit/8.2.2     (chem)      lammps/20170331                    plumed/2.3.0        (chem)
abyss/1.9.0      (bio)       mrbayes/3.2.6            (bio)     pnetcdf/1.8.1       (io)
boost-mpi/1.60.0 (t)         ncl/6.4.0                          quantumespresso/6.0 (chem)
cdo/1.7.2        (geo)       ncview/2.1.7             (vis)     ray/2.3.1           (bio)

[removed most of the output here for clarity]

   t:        Tools for development / Outils de développement
   vis:      Visualisation software / Logiciels de visualisation
   chem:     Chemistry libraries/apps / Logiciels de chimie
   geo:      Geography libraries/apps / Logiciels de géographie
   phys:     Physics libraries/apps / Logiciels de physique
   Aliases:  Aliases exist: foo/1.2.3 (1.2) means that "module load foo/1.2" will load foo/1.2.3
   D:        Default Module

Use "module spider" to find all possible modules.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
```
{: .output}

## Loading and unloading software

To load a software module, use `module load`.
In this example we will use fastqc.

Initially, fastqc is not loaded. 
We can test this by using the `which` command.
`which` looks for programs the same way that Bash does,
so we can use it to tell us where a particular piece of software is stored.

```
[remote]$ which fastqc
```
{: .bash}
```
/usr/bin/which: no fastqc in (/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/dell/srvadmin/bin:/home/yourUsername/.local/bin:/home/yourUsername/bin)
```
{: .output}

We can load the `fastqc` command with `module load`:

```
[remote]$ module load fastqc
[remote[$ which fastqc
```
{: .bash}
```
/cluster/spack/opt/spack/linux-centos7-x86_64/gcc-4.8.5/fastqc-0.11.5-dtjwde43z7ktbhq7pwc5btlzqf4ksuta/bin/fastqc
```
{: .output}

So what just happened?

To understand the output, first we need to understand the nature of the `$PATH` environment
variable. `$PATH` is a special environment variable that controls where a UNIX system looks for
software. Specifically `$PATH` is a list of directories (separated by `:`) that the OS searches
through for a command before giving up and telling us it can't find it. As with all environment
variables we can print it out using `echo`.

```
[remote]$ echo $PATH
```
{: .bash}
```
/cluster/spack/opt/spack/linux-centos7-x86_64/gcc-4.8.5/fastqc-0.11.5-dtjwde43z7ktbhq7pwc5btlzqf4ksuta/bin:
/cluster/spack/opt/spack/linux-centos7-x86_64/gcc-4.8.5/perl-5.24.1-wgrwtsrr3rvn54b4i56raxnop2xynubk/bin:
/cluster/spack/opt/spack/linux-centos7-x86_64/gcc-4.8.5/gdbm-1.13-ssxfxqt5e4vnxgogdpbiwymgmybk4iwd/bin:
/cluster/spack/opt/spack/linux-centos7-x86_64/gcc-4.8.5/readline-7.0-elgoxoqsat4xt56tgfp2obo4odhohhdk/bin:
/cluster/spack/opt/spack/linux-centos7-x86_64/gcc-4.8.5/ncurses-6.0-zxqghp37j45pyopzyqzpjmi4xqvn33le/bin:
/cluster/spack/opt/spack/linux-centos7-x86_64/gcc-4.8.5/jdk-8u141-b15-y6l4xaepdukhuxn3vxb7mo26pqmcb6ln/bin:
/usr/local/bin:
/usr/bin:
/usr/local/sbin:
/usr/sbin:
/opt/dell/srvadmin/bin:
/home/yourUsername/.local/bin:
/home/yourUsername/bin
```
{: .output}

You'll notice a similarity to the output of the `which` command. In this case, there's several additional paths: with `/cluster/spack/opt/` directory at
the beginning. When we ran `module load fastqc`, it added this directory to the beginning of
our `$PATH`. Let's examine what's there:

```
[remote]$ ls /cluster/spack/opt/spack/linux-centos7-x86_64/gcc-4.8.5/fastqc-0.11.5-dtjwde43z7ktbhq7pwc5btlzqf4ksuta/bin
```
{: .bash}
```
fastqc
```
{: .output}

Taking this to it's conclusion, `module load` will add software to your `$PATH`. It "loads"
software. A special note on this - depending on which version of the `module` program that is
installed at your site, `module load` will also load required software dependencies. To 
demonstrate, let's use `module list`. `module list` shows all loaded software modules.

```
[remote]$ module list
```
{: .bash}
```
Currently Loaded Modules:
  1) jdk/jdk-8u141-b15       3) readline/readline-7.0   5) perl/perl-5.24.1
  2) ncurses/ncurses-6.0     4) gdbm/gdbm-1.13          6) fastqc/fastqc-0.11.5

```
{: .output}

So in this case, loading the `fastqc` module , also loaded
`jdk/jdk-8u141-b15` and `eadline/readline-7.0` as well. Let's try unloading the `fastqc` package.

```
[remote]$ module unload fastqc
[remote]$ module list
```
{: .bash}
```
Currently Loaded Modules:
  1) jdk/jdk-8u141-b15       2) ncurses/ncurses-6.0     3) readline/readline-7.0   4) gdbm/gdbm-1.13          5) perl/perl-5.24.1
```
{: .output}

So using `module unload` "un-loads" a module.
If we wanted to unload everything at once, we could run `module purge` (unloads everything).

```
[remote]$ module purge
```

> ## Using software modules in scripts
>
> Create a job that is able to run `fastqc --version`. Remember, no software is loaded by default!
> Running a job is just like logging on to the system (you should not assume a module loaded on the
> login node is loaded on a worker node).
{: .challenge}

> ## Loading a module by default
> 
> Adding a set of `module load` commands to all of your scripts and having to manually load modules
> every time you log on can be tiresome. Fortunately, there is a way of specifying a set of 
> "default  modules" that always get loaded, regardless of whether or not you're logged on or 
> running a job. Every user has two hidden files in their home directory: `.bashrc` and 
> `.bash_profile` (you can see these files with `ls -la ~`). These scripts are run every time you 
> log on or run a job. Adding a `module load` command to one of these shell scripts means that 
> that module will always be loaded. Modify either your `.bashrc` or `.bash_profile` scripts to 
> load a module like fastqc. Does your `fastqc --version` job from before still 
> need `module load` to run?
{: .challenge}

## Installing software of our own

Most HPC clusters have a pretty large set of preinstalled software. Nonetheless, it's unlikely that
all of the software we'll need will be available. Sooner or later, we'll need to install some
software of our own.

Though software installation differs from package to package, the general process is the same:
download the software, read the installation instructions (important!), install dependencies,
compile, then start using our software.

As an example we will install the bioinformatics toolkit `seqtk`. We'll first need to obtain the
source code from GitHub using `git`.

```
[remote]$ git clone https://github.com/lh3/seqtk.git
```
{: .bash}
```
Cloning into 'seqtk'...
remote: Counting objects: 316, done.
remote: Total 316 (delta 0), reused 0 (delta 0), pack-reused 316
Receiving objects: 100% (316/316), 141.52 KiB | 0 bytes/s, done.
Resolving deltas: 100% (181/181), done.
```
{: .output}

Now, using the instructions in the README.md file, all we need to do to complete the install is to
`cd` into the seqtk folder and run the command `make`.

```
[remote]$ cd seqtk
[remote]$ make
```
{: .bash}
```
gcc -g -Wall -O2 -Wno-unused-function seqtk.c -o seqtk -lz -lm
seqtk.c: In function ‘stk_comp’:
seqtk.c:399:16: warning: variable ‘lc’ set but not used [-Wunused-but-set-variable]
    int la, lb, lc, na, nb, nc, cnt[11];
                ^
```
{: .output}

It's done! Now all we need to do to use the program is invoke it like any other program.

```
[remote]$ ./seqtk
```
{: .bash}
```
Usage:   seqtk <command> <arguments>
Version: 1.2-r101-dirty

Command: seq       common transformation of FASTA/Q
         comp      get the nucleotide composition of FASTA/Q
         sample    subsample sequences
         subseq    extract subsequences from FASTA/Q
         fqchk     fastq QC (base/quality summary)
         mergepe   interleave two PE FASTA/Q files
         trimfq    trim FASTQ using the Phred algorithm

         hety      regional heterozygosity
         gc        identify high- or low-GC regions
         mutfa     point mutate FASTA at specified positions
         mergefa   merge two FASTA/Q files
         famask    apply a X-coded FASTA to a source FASTA
         dropse    drop unpaired from interleaved PE FASTA/Q
         rename    rename sequence names
         randbase  choose a random base from hets
         cutN      cut sequence at long N
         listhet   extract the position of each het
```
{: .output}

We've successfully installed our first piece of software!

{% include links.md %}
