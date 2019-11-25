Updated: November 24, 2019

## Learning Objectives
- Connect to HPC at UCI
- Use common commands
    - [cd](#cd), [ls](#ls), [pwd](#pwd), [mkdir](#mkdir), [cp](#cp), [mv](#mv), [rm](#rm), [module](#Using-common-software)
- [View files](#Examining-Files) 
    - cat, less, more, tail

## Important Notes

- HPC is a shared facility that is transitioning to HPC3. *What does that mean for you?* At the moment nothing. 
- There are over 2000 users and at any one time there could be 100+ users online so don't use it as your personal machine. 
- To access HPC you'll have to be using UCI's network (be on campus) or download a VPN client (for off campus connection) following these [instructions](https://www.oit.uci.edu/help/vpn/). 
- It is not your personal storage hard drive. Once you exceed your limits you will be sent a warning before HPC admins start deleting your files. 

### Logging in Prep

If you don't already have an account, you can request one following these [instructions](https://hpc.oit.uci.edu/signup.html). 

**With Macs**

Macs have a utility application called "**Terminal**" for performing tasks on the command line (shell), both locally and on remote machines. To find terminal go to Applications -> Utilities -> Terminal app. There are alternative apps if you wish to use them such as [iterm2](https://iterm2.com).

**With Windows**

By default, there is no terminal for the bash shell available in the Windows OS, so you have to use a downloaded program, [Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) to log in to remote machines from Windows computers.

**With Linux**
Use one of the many terminals available to you. 

### Log In 

You must use `ssh`, an encrypted terminal protocol. Be sure to use the `-Y` or `-X` options, if you want to view X11 graphics

Type in the following command with your username to login:

```bash
ssh -Y UCINetID@hpc.oit.uci.edu
# the '-Y' requests that the X11 protocol is tunneled back to you, encrypted inside of ssh.
```

You will receive a prompt for your password, and you should type in your  password; note that the cursor will *not move* as you type in your password so press *Enter* when done.

### First Step

The login node(s) should be considered your 1st stop in doing real work. You can copy files to and from your home directory via the login node, edit files, compile and test code, etc, but you **shouldn't** run any long (>1 hr) jobs on the login node itself. If you do your job might be killed by HPC administrators. To do real work, please request a node from the interactive queue, like this:

```bash
UCINetID@hpc-login-1-2:~ qrsh
```

If you know which queues you have access to you can request a specific queue like this:

```bash
qrsh -q som
```
If you don't know which queues you have access to then try this to get a list of them.

```bash
q
```

### Most common commands

#### cd

Once you log in you will probably be at the following location:

```bash
/data/users/UCINetID
```

This location has a data storage limit, which means you can't save that many files here. You should change directoy to a place where you have a higher storage limit. For example:

```bash
cd /df3/som/UCINetID
```

In the example above **som** is the queue/compute node the user has access to, so replace that with what ever location you have access to. It usually depends on your PI's department. Other posibilities include:

> bio, som, pub

`cd` stands for 'change directory' and you will use this command to move around between different directories ( think of directories as folders). 

#### ls
Often times when you change directories you want to see a list of files or sub directories that are located there so we can list everyting with this command:

```bash
ls
```

Output:

```
genomics_data
other
raw_fastq
README.txt
reference_data
```

`ls` will list all the contents in your current directory. Several options can be added to the command `ls` which will change how the output looks. For example `ls` will just list out all files and directories, but sometimes you want to see their size:

```bash
ls -lh
```

- The `-l` is an option that will return a long list that include the permissions, owner, group, size, and date of the file/directory.
- The `-h` option will print the size in a human readable format (e.g., 1K 234M 2G).

Output:
```
drwxrwsr-x 2 UCINetID UCINetID  78G Sep 30 10:47 genomics_data
drwxrwsr-x 6 UCINetID UCINetID 107K Sep 30 10:47 other
drwxrwsr-x 2 UCINetID UCINetID 228G Sep 30 10:47 raw_fastq
-rw-rw-r-- 1 UCINetID UCINetID 377M Sep 30 10:47 README.txt
drwxrwsr-x 2 UCINetID UCINetID 238G Sep 30 10:47 reference_data
```

Try without the `-h` and see what you get.

Lets pretend that we are in the following directory

```
/df3/som/UCINetID/genomics_data
```

but we want to go up one directory to 

```
/df3/som/UCINetID/
```

but instead of writing out the the complete path you can move up with this shortcut:

```bash
cd ..
```
Try it. 

#### pwd
If you ever forget which directory you are in you can always check with:

```bash
pwd
```

`pwd` stands for 'print working directory'.

#### mkdir
Many times you'll want to make a new directory/sub directory.

```bash
mkdir DIRECTORY_NAME
```
`mkdir` will make a new direcotry in your current working directory. If your directory name includes multiple words, **do not** add spaces and instead use '_'.

#### cp
Copying files or directories can be done with `cp`

```bash
cp README.txt /df3/som/UCINetID/genomics_data/README.txt
```
The command above would take the README.txt file that is located at /df3/som/UCINetID/ and make a copy at the new location /df3/som/UCINetID/genomics_data with the same name. 

#### mv
Move/rename files from one directory to another using `mv`.

Before beginning you must be in the directory where your file is located. If you just want to rename a file your command would look like this:
```bash
mv README.txt READ.txt
```
The original README.txt file will no longer exist and instead you should see READ.txt.

If you wish to move a file from one location to another then your code would look like:
```bash
mv README.txt /df3/som/UCINetID/genomics_data/README.txt
```
The command above would take the README.txt file that is located at /df3/som/UCINetID/ and move it to the new location /df3/som/UCINetID/genomics_data with the same name. 

#### rm
Permanently remove a file/directory (careful it can't be undone).
For a file:
```bash
rm README.txt
```
For a directory:
```bash
rm -r genomics_data
```
genomics_data is a directory from the example above. 

The options `-i` and `-r` can be useful to you. 
- `-r`: recursive, commonly used as an option when working with directories. 
- `-i`: prompt before every removal.

#### Using common software

You might be intersted in using some common software on HPC such as R, Python, samtools, tophat, hisat2, STAR, salmon, rsem, bwa, etc. and you **do not** need to install them because many of them are already installed. If there is something that is not installed then you should contact HPC administrators. To see a list of all the software available try this:

```bash
module avail
```

and you'll get an output similar to this:

```
-------------------------- /data/modulefiles/SOFTWARE --------------------------
454/2.8                               Mathematica/8.0.0
7zip/16.02                            matio/1.5.6
7zip/9.20.1                           MATLAB/r2011b
abaqus/6.14                           MATLAB/r2014a
abaqus/6.20                           MATLAB/r2014b
abinit/7.08.1                         MATLAB/r2015a
abinit/7.10.2                         MATLAB/r2016a
abinit/7.10.4                         MATLAB/r2017a
abinit/7.10.5                         MATLAB/r2017b
abyss/1.3.2                           MATLAB/r2018a
abyss/1.5.2                           mats/3.0.7b
abyss/1.9.0                           mats/3.0.9
AdmixTools/06-12-2018                 mats/3.2.5
admixture/1.3.0                       mats-turbo/ucs2
afni/2011_12_21_1014                  mats-turbo/ucs4
afni/v17.2.05                         maven/3.3.3
afni/v17.2.16                         MaxBin/2.2.1
afni/v17.3.07                         MCL/14.137
```

To load a module try this:

```bash
module load Mathematica/8.0.0
```

After the command, insert your software with the version number. Notice how MATLAB has multiple versions so you can use any one of them. If you do not include the version then it will load the newest version (most of the time). 

Similarly you can unload modules with:

```bash
module unload Mathematica/8.0.0
```

You can check which modules have you loaded with:

```bash
module list
```

and it will output all of the modules that you have loaded and are ready to use

```
Currently Loaded Modulefiles:
  1) gcc/6.4.0          2) Cluster_Defaults
```

### Examining Files

Now that you know how to move around your directories and see what is in directoires, we can now view content of files. 

In the earlier example, there was a *README.txt* file which we want to look at. The first way of viewing the file is by using the `cat` command. 

```bash
cat README.txt
```

Output:
```
Hello World!
```

`cat` is terrific command for small files, but when the file is really big it can be annoying to use. Most likely you will be dealing with large files if you are doing bioinformatics. The command, `less`, is useful for this case. 

```bash
less README.txt
```
Your ouput will be in a pseudo-window where you can now scroll through the large file. The keys used to move around the file are shown below.

<span class="caption">Shortcuts for `less`</span>

| key              | action                 |
| ---------------- | ---------------------- |
| <kbd>SPACE</kbd> | to go forward          |
| <kbd>b</kbd>     | to go backwards        |
| <kbd>g</kbd>     | to go to the beginning |
| <kbd>G</kbd>     | to go to the end       |
| <kbd>q</kbd>     | to quit                |

`less` also gives you a way of searching through files. Just hit the <kbd>/</kbd> key to begin a search. Enter the name of the string of characters you would like to search for and hit enter. It will jump to the next location where that string is found. If you hit <kbd>/</kbd> then <kbd>ENTER</kbd>, `less` will just repeat the previous search. `less` searches from the current location and works its way forward. If you are at the end of the file and search for the word "cat", `less` will not find it. You need to go to the beginning of the file and search.

If you just want to view a portion suchas as the beginning or end of a file you can use `head` and `tail`, respectively.