# HPC Workshop
_by Daniel Pedersen_

## Setup ssh
```bash
# generate a rsa keypair in ~/.ssh
# id_rsa contains your private key and should be kept secret at all costs
# id_rsa.pub contains your public key and can be shared without risk
# when prompted for password, leave it empty
ssh-keygen -t rsa -b 4096 -C "<email>"

# copy the public key to spartan to allow login with key instead of password
ssh-copy-id <username>@spartan.hpc.unimelb.edu.au

# configure ssh so you can just type "ssh spartan"
# this is just for convenience (I'm very lazy)
cd ~/.ssh
echo "Host spartan" > config
echo "  Hostname spartan.hpc.unimelb.edu.au" >> config
echo "  User <username>" >> config
echo "  IdentityFile ~/.ssh/id_rsa" >> config
```

## Syncing stuff to spartan
```bash
# create a project folder that we will keep synced to spartan
cd ~
mkdir projects

# create a test project
cd projects
mkdir test-project

# sync everything to spartan
rsync -avz ./ spartan:~/projects
```

<div style="page-break-after: always;"></div>

## Example: run R script
```bash
# go to projects directory
> cd ~/projects
# create r-test directory and cd into it
> mkdir r-test
> cd r-test
# create batch file and set executable permission
> touch run
> chmod +x run
```

Now let's create an R file, call it `test.r`:
```r
data <- c(1, 2, 3)
print(data)
```

<div style="page-break-after: always;"></div>

Now let's edit our batch script at `~/projects/R-test/run`:
```bash
#!/usr/bin/env bash
# SBATCH --time <timeout>

# the first line sets up your environment and says this file is a bash script
# the second line sets flags for the batch job

# load the R command
module load R

# run R script in bash mode
srun Rscript test.r out.txt
```

Now, lets sync this project to spartan
`> rsync -avz ~/projects/ spartan:~/projects`

Then, login to spartan, and run the job
`> ssh spartan`
`[spartan] > sbatch projects/r-test/run`

There should be a file named `out.txt` and `slurm-XXXXXXX.out` placed in the directory.
`out.txt` is the output from R, and `slurm-XXXXXXX.out` contains the output from the bash script.


<div style="page-break-after: always;"></div>

## Important commands
|        **To do this**        |                **Run this**                |
|:----------------------------:|:------------------------------------------:|
|       login to spartan       |               `ssh spartan`                |
|       list a directory       |               `ls -Al <dir>`               |
|        create a file         |             `touch <filename>`             |
| change permissions on a file |        `chmod <change> <filename>`         |
|    make a file executable    |           `chmod +x <filename>`            |
|   sync projects to spartan   | `rsync -avz ~/projets/ spartan:~/projects` |
|      show file contents      |             `less <filename>`              |
|   run batch script on node   |            `sbatch <filename>`             |
|       check run squeue       |           `squeue -u <username>`           |
|     run command on node      |         `srun <command or script>`         |
