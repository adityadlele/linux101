# How to use the computing cluster

** 1. Getting started **
 
You can use the research computing website for a [comprehensive guide](https://researchcomputing.princeton.edu/get-started/guide-princeton-clusters)

or 

A shorter version:
- Access the compueter cluster using a command prompt. [Click here](https://researchcomputing.princeton.edu/support/knowledge-base/connect-ssh)
- Access the cluster using web. **(I highly reccomend this if you are a beginer)**
To use the Open OnDemand web interface for Adroit, Della, or Stellar you must first have an account set-up for the cluster you wish to use. 
(For instructions on how to gain access, navigate to Adroit's(**for this class**) or other clusters webpage in the Systems submenu of the Research Computing site.) 
Once you have an account, you can browse to [Adroit](https://myadroit.princeton.edu).

Note that to access these links from off-campus you will need to use a [VPN](https://princeton.service-now.com/snap?id=kb_article&sys_id=ce2a27064f9ca20018ddd48e5210c745).

- The computer clusters runs Linux. So you will need learn basics of [linux](https://github.com/gabeclass/introcmdline).


**2. File system **

There are two types of file storages available for all the clusters. For adroit
1. /home/<YourNetID> **(You can store imporatant results here!)**
* For - source code and executables
* Details - This folder is small and it is intended for storing source code, executables, Conda environments, R or Julia packages and small data sets.

2.  /scratch/network/<YourNetID> **(You will run your jobs here!)**
* For - job output, intermediate results
* Details - This folder is a fast, parallel filesystem that is local to each cluster which makes it ideal for storing job input and output files. However, because /scratch/gpfs is not backed up you will need to transfer your completed job files to /tigress or /projects when a backup is desired.
For more information us the following [link](https://researchcomputing.princeton.edu/support/knowledge-base/data-storage).

**3. Using Python **

We will be using Jupyter notebooks for first few classes. Jupyter notebook allows us to interactively use Python. First you should use following command in your *home* directory. 
```sh
module load anaconda3/2021.11
conda create --name myenv <package-1> <package-2> ... <package-N>
```
If you are already familiar with Python or interested in further exploring it use the following [link](https://researchcomputing.princeton.edu/support/knowledge-base/python) for instructions on using Python on Princeton clusters 

We will use Jupyter notebook to access the above conda environment. Please follow the research computing [instructions](https://researchcomputing.princeton.edu/support/knowledge-base/jupyter#ondemand) to run Jupyter on your web-browser. Make a python notebook and name it *test* with the following code 

   

```sh
Python test
```
