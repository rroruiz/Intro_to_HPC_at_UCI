Updated: December 2, 2020

## Learning Objectives
- Differentiate between free and accounted jobs
- Submitting jobs

## Overview

"Submitting a job"" refers to a script that a user has made and it contains all the commands required to complete the task you whish the computer to do. When you submit your job, the computer will run it for you so that you can go make better use of your time.  

HPC3 uses the Slurm scheduler, which is widely used at super computer centers. Many of HPC's SGE concepts are available in Slurm. A [cheat sheet](https://slurm.schedmd.com/pdfs/summary.pdf) for using Slurm is available and was made by the Slurm developers.

As mentioned in the Intro to HPC3 lesson, jobs can be submitted as **accounted** or **free**. Every HPC3 user has 1000 free CPU hours as a startup package but once they are used up they are gone forever. When you use up your 1000 CPU hours then you will either have to submit jobs to the free option or have your PI/center pay a fee by using the accounted option. We will look at examples of how to submit free and accounted jobs later. 

To check how many CPU hours you have left you can check by using the following:

```bash 
[user@login-x:~]$ sbank balance statement -a panteater
User           Usage |        Account     Usage | Account Limit Available (CPU hrs)
---------- --------- + -------------- --------- + ------------- ---------
panteater*        58 |      PANTEATER        58 |         1,000       942
```
In the example above, PANTEATER has used 58 CPU hours out of his 1000.

### Free Jobs

Free Jobs do not get charged to an account but your job does not have priority. What does that mean? Any free job can be killed by a paid job if there are not enough cores for someone who is paying. In other words if you pay then you are granted cores and you can take them from someone who didn't pay. I do not recommend that you submit free jobs if your job (i.e. genome alignment) and time are valuable. If your job gets killed you will have to start the job over from the beginning. 

### Paid/Accounted Jobs

Any job submitted to a ***standard*** partition is considered an accounted/paid job. If you pay for your job (i.e. genome alignment), then it will run to completion and won't be killed. A standard job has more priority than a free job. You'll have the option of choosing between three QoS (Quality of Service): Low, Normal, or High. Low and Normal get charged for the time that is used. High, on the other hand, gets charged double the CPU consumed but the benefit is that your job goes to the front of the line. This would be a good option when you have a grant that is due tomorrow and you needed the data yesterday (in other words you need the data ASAP). If you want to know how an account is charged please visit the [RCIC site](https://rcic.uci.edu/hpc3/slurm.html#_how_accounts_are_charged)

### Submitting your job

Submitting a job is as easy as using `sbatch` followed by the script file. 
```bash
sbatch myscript.sub
```
Lets take a look at what should be in `myscript.sub`

```bash
#!/bin/bash

#SBATCH --job-name=test      ## Name of the job.
#SBATCH -A panteater_lab     ## account to charge 
#SBATCH -p standard          ## partition/queue name
#SBATCH --nodes=1            ## (-N) number of nodes to use
#SBATCH --ntasks=1           ## (-n) number of tasks to launch
#SBATCH --cpus-per-task=1    ## number of cores the job needs
#SBATCH --error=slurm-%J.err ## error log file

# Run command hostname and save output to the file out.txt
srun hostname > myFile.txt
```
The `myscript.sub` will run the command `hostname` and save the output to the file `myFile.txt`
You need to have all the #SBATCH options or your script won't run. The ## notes on the right hand side provide a breif explanation of what each line means. 
Below is another example

```bash
#!/bin/bash

#SBATCH -p standard                   ## run on the standard partition
#SBATCH -N 1                          ## run on a single node
#SBATCH -n 1                          ## request 1 task (1 CPU)
#SBATCH -t 2:00:00                    ## 2 hr run time limit
#SBATCH --mail-type=end               ## send email when the job ends
#SBATCH --mail-user=UCInetID@uci.edu  ## use this email address

module purge
module load python/3.8.0
python HelloWorld.py
```
The above will submit the Python 3 code `HelloWorld.py` with requested resources. In this job we are asking the machine to send us an email when the job ends. Any emails will be sent to the email address you put unser --mail-user option.

Once you submit your job you should see something similar to the bottom examples:
```bash
[user@login-x:~]$ sbatch myscript.sub
Submitted batch job 362
```
You can check the status of your job by using `squeue` command. Let's take a look.

```bash
[user@login-x:~]$ squeue -u panteater
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
               362  standard     test panteater R       0:03      1 hpc3-17-11
```
Make sure to use the `-u` option followed by your UCINetID so that you only see your jobs. 

If you do not wish to submit your job but would rather run an interactive job (a job where you are interacting with the console [i.e. using IGV]) you can use 

```bash
[user@login-x:~]$ srun -A panteater --pty bash -i
```
The command above will only work if you are enabled to charge the panteater account. The `-A` option is specifiying which account you want to charge. If you wish to not charge an account and would like to use the free option discussed above then use this command (reminder that it can be killed)

```bash
[user@login-x:~]$ srun -p free --pty bash -i
```
Sometimes you may realize that you made a mistake in your code and you need to kill the job. To do that use the following options: 

```bash
[user@login-x:~]$ scancel <jobid>
```
Remember that you can can get the `<jobid>` by running `squeue` as shown above. This will cancel that specific job. If you wish to cancel all your jobs that are running you can use the following:

```bash
[user@login-x:~]$ scancel -u panteater
```
The command above it will cancel all the jobs running by user Panteater. 




