[Compute Canada](https://www.computecanada.ca/) (CC) provides fantastic computational capabilities to many researchers in [Canada](https://www.canada.ca/en.html). It uses [Slurm](https://slurm.schedmd.com/documentation.html) for managing tasks and is well documented. 
However, depending on your use-case, it could be confusing, and you may spend a lot of time to fetch the relevant information in order to set your workflow up.  
In this repository we are going to walk through setting of a deep learning research workflow using [PyTorch](https://pytorch.org/).

# Registration
The first step is to apply for a CC account. If you are a PI, you should apply to get a Compute Canada Role Identifier (CCRI). 
If you are a group member (student, researcher working under a supervisor, etc.), you should ask your supervisor's for thier CCPI and use that to apply for an account. 
The essential steps for registering are explained [here](https://www.computecanada.ca/research-portal/account-management/apply-for-an-account/#:~:text=PIs%20must%20register%20with%20the%20CCDB%20first.%20The,that%20includes%20your%20Compute%20Canada%20Role%20Identifier%20%28CCRI%29.).
Once you got registered you can use your username and password to log into CC website or ssh into one of the [clusters](https://www.computecanada.ca/clusters/). 

# Authentication
Here is how you can register your ssh key.
If on Windows, first make sure that **OpenSSH Client** is activated from Optional features else:
1. Generate an ssh-key in your local computer 
   2. open a terminal and type `ssh-keygen` followed by enters (if asked for setting up passcode you can leave blank, i.e., just enter).
   3. There should be a pair of keys (stored in files) generated. By default, they are `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`. The second one (public key) is the one that you should provide to the other party for authentication.
   4. Go to your CC account in your browser and navigate to **My Account/Manage SSH Keys**. There is a textbox on this page to paste your public key. 
   5. Open your public key file (the file ending with `.pub`) with a text editor. Copy its content and paste in the text box of step 4.
   6. give it a friendly name using the description textbox and hit on **Add Key**.
   7. Now go back to terminal on your local machine and try to ssh. Voilà! You shouldn't be asked for password anymore!

Let's make our life even easier! How does it sound typing `ssh graham` instead of `ssh username@graham.computecanada.ca`? To do this you should create a text file under `~/.ssh/` and name it "config" (if it doesn't already exist). **Notice to Windows users:** name of this file should not end with an extension such as ".txt".
Now all you need to do is appending the following lines to the file:


```
Host graham
  HostName graham.computecanada.ca
  User your_username
  IdentityFile ~/.ssh/id_rsa
```

This was just for graham, you can add for other clusters that you have access to as well. Now try it: `ssh graham`.


# Command Sheet
| N | purpose                        | Command                                              | Description                                      |
|---|--------------------------------|------------------------------------------------------|--------------------------------------------------|
| 1 | load python module             | `module load python/3.9`                             | replace python version (3.9) to your desired     |
| 2 | make a virtual environment     | `virtualenv $SCRATCH/predefined_envs/myenv`          | replace environment name (myenv) to your desired |
| 3 | activate a virtual environment | `source $SCRATCH/predefined_envs/myenv/bin/activate` | replace environment name (myenv) to your desired |



# Storage
You can find out about the storage and file management in CC [here](https://docs.computecanada.ca/wiki/Storage_and_file_management#:~:text=When%20your%20account%20is%20created%20on%20a%20Compute,to%20these%20other%20filesystems%20from%20your%20home%20directory.);
however, the rule of thumb regarding storage on CC is to 
- keep your large datasets on `$PROJECT` (slow storage).
- use `$SCRATCH` for things that you want to change frequently (experiment logs and outputs). I even keep my project files on this storage.
- if you want to create files only to exist for the duration that a task is running use `$SLURM_TMPDIR`. Whatever made is purged after the job is finished or terminated. This is super useful for making temporary virtualenv. 
The rest of directories are not essential for any particular use-case as far as we know.


# Networking
Some clusters do not let you access to the internet in their compute nodes (Graham, Beluga, ...) and some other do (Cedar, ...). You would get an error if your program tries to do so.  

# Visualization

## Tensorboard
[Tensorboard](https://www.tensorflow.org/tensorboard/) is one of the most popular tools for visualizing/monitoring the experiments.  
The following are the steps you should take for the **first time running** tensorboard on a cluster in CC:
1. ssh into your cc account.
2. load python module (N=1 on Command Sheet) 
3. make a virtual environment (N=2 on Command Sheet)
4. activate the virtual environment (N=3 on Command Sheet)
5. `pip install tensorboard`

The following are the steps you should take each time you want to use tensorboard.
1. ssh into your cc account.
2. activate the virtual environment (N=3 on Command Sheet)
3. `cd $SCRATCH` (this is because you cannot submit jobs from your home)
4. submit a minimal **interactive job**, `salloc --time=1:0:0 --ntasks=1 --cpus-per-task=4 --mem-per-cpu=2048M --account=<cc account identifier often starts with "def-">`
5. launch tensorboard `tensorboard --logdir=<your_log_dir> --host 0.0.0.0 --load_fast false` (replace `<your_log_dir>` with the logdir of your project). 
6. At this step you should see an url in your terminal, an example is "http://cdr774.int.cedar.computecanada.ca:6006". In this url "cdr" stands for **cedar** (because we logged into cedar in step 1) and 774 is the node number that we have allocated our resources from (basically we are running commands from that node or to make it simpler from that computer).
7. go to your local computer, open your terminal (no matter Windows. MacOS or Linux) and tunnel to the node that is running the service through the provided port. In the case of our example that is `ssh -L 6006:cdr767.int.cedar.computecanada.ca:6006 username@cedar.computecanada.ca`. In case you have setup the ssh config based on our recommendation you can enter a shorter command `ssh -L 6006:cdr767.int.cedar.computecanada.ca:6006 cedar`.

## MLFlow
To be completed!

