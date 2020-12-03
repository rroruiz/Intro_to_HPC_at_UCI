Updated: December 3, 2020

## Learning Objectives
- Write your first script/job

## Overview

- A script is a file that contains series of commands and instructions that the computer will read and execute. We will look at some examples below. Please review Intro to HPC3 prior to going through this lesson as it builds on what you learn there. 

## Script
There are several ways to write your script. If you are a beginner you might want to use a text editor (e.g. Atom, Notepad++, Visual Studio Code) on your computer. If you are a more advanced user you can consider using nano or Vim on the command line. Then again there are many options and you can use which every text editor you find on google.

These examples will use nano on HPC3. 

First log into HPC3 (remember that if you are off campus you need to use VPN provided by UCI. Instuctions for downloading the VPN can be found [here](https://www.oit.uci.edu/help/vpn/)). In order to log in to HPC3, open your terminal (on a mac), putty (on windows) or your favorite command line tool for linux (a review on how to do this is found in the "Intro to HPC3" tutorial).

```bash
# Replace panteater with your UCINetID
ssh -Y panteater@hpc3.rcic.uci.edu
```
The following will appear.   
You'll be prompt to input your password unless you set it up for paswordless login.
```bash
panteater@hpc3.rcic.uci.edu password: 
Last login: Wed Dec  2 12:44:43 2020 from 10.XXX.X.XX
+-----------------------------------------+
|  _             _             _ _  __    |
| | | ___   __ _(_)_ __       (_) |/ /_   |
| | |/ _ \ / _` | | '_ \ _____| | | '_ \  |
| | | (_) | (_| | | | | |_____| | | (_) | |
| |_|\___/ \__, |_|_| |_|     |_|_|\___/  |
|          |___/                          |
+-----------------------------------------+
 Distro:  CentOS 7.8 Core
 Virtual: NO

 CPUs:    40
 RAM:     191.9GB
 BUILT:   2020-03-03 16:13
[panteater@login-i16:~] $
```
Change directory to where you save all your files. Panteater saves its files on /dfs4/som . You need to determine where you are allowed to save your files. Typically if you are part of School of Medicine you can save at /dfs4/som/UCINetID/, if you are part of School of Biological Science you can save at /dfs5/bio/UCINetID, if you are MCSB student often times you can save at /dfs6/pub/UCINetID . Don't take my word for it and figure out where you can save. In this case panteater is part of the school of medicine.
```bash
cd /dfs4/som/panteater/
```
 
 Next we want to start making our script. 
 
```bash
 touch myscript.sub # make a file called myscript.sub
 nano myscript.sub # open the file file with nano
```
 In the script we need specific information and the commands that you want to run. For this example, we will pretend that panteater did some single cell RNA sequencing and he wants to submit a job that aligns his reads. This script should contain the following information and commands  
   
```bash
 #!/bin/bash

#SBATCH --job-name=cell_ranger_counts      ## Name of the job.
#SBATCH -A panteater_lab                   ## account to charge 
#SBATCH -p standard                        ## partition/queue name (you can choose low/standard/high)
#SBATCH --nodes=1                          ## (-N) number of nodes to use
#SBATCH --ntasks=1                         ## (-n) number of tasks to launch
#SBATCH --cpus-per-task=32                 ## number of cores the job needs
#SBATCH --error=slurm-%J.err               ## error log file
#SBATCH --mail-type=end                    ## send email when the job ends
#SBATCH --mail-user=panteater@uci.edu      ## use your UCI email address for notifications

cd /dfs4/som/panteater                      ## change directory to where you want to save the alignment
mkdir RNA_seq_results                       ## make a directory where I want to save the data
cd /RNA_seq_results                         ## change directory 

module load cellranger/3.1.0                ##load the software you need for alignment
cellranger count --id=Control1 \                              ## id will create a folder with that name
                 --fastq=/PATH/TO/FASTQ/FILES/ \              ## path to your fastq files
                 --sample=SampleNameFromFASTQFiles            ## sample name of fastq file, usually the first part of the file name before the first underscore.
                 --transcriptome=/PATH/TO/TRANSCRIPTOME       ## Transcriptomes provided by cellranger can be found at /dfs6/commondata/
cellranger count --id=Tumor1 \                                ## Once your first cellranger counts finishes it will move to this next command. 
                 --fastq=/dfs4/som/panteater/FASTQ_files/ \  
                 --sample=S1 \  
                 --transcriptome=/dfs6/commondata/refdata-cellranger-mm10-1.2.0
```
`cellranger counts` will perform the alignment and generate a count matrix for panteater so that s/he can do downstream analysis on their computer. 

Once you finish writing your script you will press ^X (it's the same as control + X) to exit. It will ask you if you want to save the changes so just follow the prompts (y for yes or n for no). You'll get the opportunity to change the file name (originally you called it `myscript.sub`) if you wish. `Control + E` will exit and save but if you just want to save without exiting then you can use `Control + O`. 


Check your location
```bash
pwd         
```
Output
```bash
/dfs4/som/panteater/RNA_seq_results/
```
Now you can check your files
```bash
ls
```
Output
```bash
myscript.sub
```

If everything looks good now you can submit your job. 
```bash
sbatch myscript.sub
```
Your script will be sent to the queue and will start when it's your turn. Now you wait until it finishes. Assuming you didn't make mistakes in your directory paths everything should work perfect. 


Now try making your own script to do specific tasks that you need to get done.

 