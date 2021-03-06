# Getting Started

This page is intended as a brief introduction into HPC and to get you up and running with Ubelix. This hands-on introduction targets primarily users without prior knowledge in high-performance computing. However, basic Linux knowledge is a prerequisite. If you are not familiar with basic Linux commands, there are many beginner tutorials available online. After reading this section you will have composed and submitted your first job successfully to the cluster. Links are provided throughout the text to point you to the user guide which contains more in-depth information on the topic.

## Request an Account

Staff and students of the University of Bern must have their Campus Account (CA) unlocked for UBELIX. External researchers that collaborate with an institute of the University of Bern must apply for a CA through that institute. See [here](account-activation-login.md) for more information getting access to UBELIX.

## Training Courses

[ScITS](http://www.scits.unibe.ch) regularly conducts introductory and advanced courses on Linux, UBELIX and other topics. Details oultined on [their pages](http://www.scits.unibe.ch/training/training_and_workshops).

*[ScITS]: Science IT Support

## Cluster Rules

As everywhere where people come together, rules are needed on Ubelix to allow for a good cooperation and to enable a positive HPC experience. Be always aware that you are working on a shared system where your behavior could have a negative impact on the workflow of other users. Please find the list of the most important rules and guidelines in our [code of conduct](../code-of-conduct.md).

## Login

To connect to the cluster, you must log in to the submit host from inside the university network (e.g. from a workstation on the campus). If you want to connect from a remote location (e.g. from your computer at home) you must first establish a VPN connection to get access to the university network. To connect from a UNIX-like system (Linux, Mac OS X) simply use a secure shell (SSH) to log in to the submit host:

```bash
ssh <username>@submit.unibe.ch
# the following is equivalent
ssh -l <username> submit.unibe.ch
```

## Welcome Home

After successful login to the cluster, your will find yourself in the directory /home/ubelix/<your_group>/<your_campus_account>/. This is your home directory and serves as the repository for your personal files, direcotries and programs. You can reference your home directory by ~ or $HOME. Your home directory is located on a shared file system. Therefore, all your files, programs and directory structure are always available on all cluster nodes and must hence not be copied between those nodes. We provide no backup service for data in your home directory. It is your own responsibility to backup important data to a private location. Disk space is managed by [quotas](file-system-quota). By default, each user has 3TB of disk space available. Keep your home directory clean by regularly deleting old data or by moving data to a private storage.

You can always print the current working directory using the pwd (print working directory) command:
```bash
pwd
/home/ubelix/test/testuser
```

## Move Data

At some point, you will probably need to copy files between your local computer and the cluster. There are different ways to achieve this, depending on your local operating system (OS). To copy a file from your local computer running a UNIX-like OS use the secure copy command (scp) on your local workstation:

```bash
scp /path/to/file <username>@submit.unibe.ch:/path/to/target_dir/
```

To copy a file from the cluster to your local computer running a UNIX-like OS also use the secure copy command (scp) on your local workstation:

```bash
scp <username>@submit.unibe.ch:/path/to/file /path/to/target_dir/
```

More information about file transfer can be found on the page [File Transfer to/from UBELIX](file-transfer.md).

## Use Software

On Ubelix you can make use of already preinstalled software or you can compile and install your own software. We use a module system to manage different versions of the same software. This allows you to focus on getting your work done instead of compilling software. E.g. to get a list of all provided versions of the GNU Compiler Collection (GCC), use:

```bash
module avail
```

To load GCC version 4.9, use:

```bash
module load gcc/4.9
```

Now, you are using this specific version of GCC:

```bash
gcc --version
gcc (GCC) 4.9.3
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

!!! attention "Scope"
    The loaded version of a software is only active in your current session. If you open a new shell you are again using the default version of the software. Therefore, it is crucial to load the required modules from within your job script.

The [Software](software) is dedicated to this topic. More information can be found there.

## Hello World

Finally, it's time for your first job. To do some work on the cluster, you require certain ressources (e.g. CPUs and memory) and a description of the computations to be done. A job consits of instructions to the scheduler in the form of option flags, and statements that describe the actual tasks. Let's start with the instructions to the scheduler:

    #!/bin/bash
    #SBATCH --mail-user=<your_email_address>
    #SBATCH --mail-type=none
    #SBATCH --partition=all
    #SBATCH --nodes=1
    #SBATCH --ntasks-per-node=1
 
    # Put your code below this line

The first line makes sure that the file is executed using the bash shell. The remaining lines are option flags used by the `sbatch` command. The page [Jobs Submission](job-management/submission.md) outlines the most important options of `sbatch`.

Now, let's write a simple "hello, world"-task:

    #!bash
    # Put your code below this line
    mkdir -p $HOME/my_first_job
    cd $HOME/my_first_job
    echo "Hello, Ubelix from node $(hostname)" > hello.txt
    sleep 120

In the first line we create a new directory 'my_first_job' within our home directory. The variable **$HOME** expands to `/home/ubelix/<your_group>/<your_username>`. In the second line we change directory to the newly created directory. In the third line we print the line "Hello, Ubelix from node <hostname_of_the_executing_node>" and redirect the output to a file named `hello.txt`. The expression **$(hostname)** means, run the command hostname and put its output here. In the forth line we wait (do nothing) for 120 seconds. This gives us some time to monitor our job later on. Save the content to a file named first.sh.

The complete job script looks like this:

    #!/bin/bash
    #SBATCH --mail-user=<your_email_address>
    #SBATCH --mail-type=end,fail
    #SBATCH --job-name="job01"
    #SBATCH --partition=all
    #SBATCH --nodes=1
    #SBATCH --ntasks-per-node=1
         
    # Put your code below this line
    mkdir -p $HOME/my_first_job
    cd $HOME/my_first_job
    echo "Hello, Ubelix from node $(hostname)" > hello.txt
    sleep 120

!!! warning "Use Correct Emai Addresss!"
    When using a mail-type other than 'none', make sure that you use a valid email address with the --mail-user option! 

## Schedule Your Job

We can now submit our first job to the scheduler. The scheduler will then provide the requested resources to the job. If all requested resources are already available, then your job can start immediately. Otherwise your job will wait until enough resources are available. We submit our job to the scheduler using the `sbatch` command:

```bash 
sbatch first.sh
```
```bash
Submitted batch job 32490640
```
If the job is submitted successfully, the command outputs a job-ID with which you can refer to your job later on.

## Monitor Your Job

You can inspect the state of our active jobs (running or pending) with the squeue command:

```bash
squeue --job=32490640
```
```no-highlight
      JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   32490640       all    job01 testuser  R       0:22      1 fnode23
```

Here you can see that the job 'job01' with job-ID 32490640 is in state RUNNING (R).
The job is running in the 'all' partition (default partition) on fnode23 for 22 seconds.
It is also possible that the job can not start immediately after submitting it to Slurm
because the requested resources are not yet available. In this case, the output could
look like this:

```bash
squeue --job=32490640
```
```no-highlight
       JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    32490640       all    job01 testuser PD       0:00      1 (Priority)
```

Here you can see that the job is in state PENDING (PD) and a reason why the job
is pending. In this example, the job has to wait for at least one higher priorised
job to run. See here for a list of other reasons why a job might be pending.

You can always list all your active (pending or running) jobs with squeue:

```bash
squeue --user=testuser
```
```no-highlight
      JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   34651451      all slurm.sh  testuser PD       0:00      2 (Priority)
   34651453      all slurm.sh  testuser PD       0:00      2 (Priority)
   29143227      empi    Rjob  testuser PD       0:00      4 (JobHeldUser)
   37856328      empi  mpi.sh  testuser  R       4:38      2 anode[041-042]
   32634559       all fast.sh  testuser  R    2:52:37      1 hnode12
   32634558       all fast.sh  testuser  R    3:00:54      1 hnode14
   32634554       all fast.sh  testuser  R    4:11:26      1 jnode08
   32633556       all fast.sh  testuser  R    4:36:10      1 jnode08
```
