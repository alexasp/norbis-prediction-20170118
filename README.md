# Installation Guide

For this course we will be using Amazon Web Services ([AWS](https://aws.amazon.com/products/)). AWS provide on-demand computing facilities. Most notably they provide servers with the powerful [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) graphics cards, which we will be using for this course.

Most of the technical aspects have been handled ahead of time, requiring only minimal setup on your part. If you would like to dig deeper or get the software running on your own computer a list of resources have been curated in the end of this guide.

**SECURITY WARNING**

The servers and material for this course havn't been made with security in mind, therefore assume that somebody could access your server, and any the data you put on it.

## Setup

You only need to set up the system once. Once setup is complete simply follow the instrictions in *Workflow*.

We have setup some Amazon Machine Images (AMI), and will be hosting them through our AWS accounts. In order to interface with the machines you will have to use AWS Command Line Interface (CLI). To get AWS CLI working we need, a Bash terminal, Python, and pip (a package manager for Python). If you are on a Mac or Linux you already have these, and you can skip to the section _Everybody_. If you are on Windows, you need to get these first.

### Windows People
If you don't have Python and pip the easiest way is to install [Anaconda](https://www.continuum.io/downloads). This will install both of them. Anaconda will also install a bunch of things you don't strictly need, but if you want to keep using the material from this course Anaconda is a great place to start. We suggest version 2.7, as most deep learning tools and examples are built for this version. The installer will ask you whether you want to add Python as a default, and whether it should be added to your environment path. You should tick both of these options.

We also need a Bash terminal. For this we will use Git Bash. Download it from [here](https://git-scm.com/downloads). [AWS CodeCommit](http://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-https-windows.html#setting-up-https-windows-install-git) supports Git versions 1.7.9 and later. We won't use the Git capabilities, we simply need to use it as a Bash terminal.

Most of the installation settings aren't that important, so just choose the defaults. However on the first dialog box, called `Select Components`, tick all the boxes, if they aren't already.

Use Git Bash as your terminal for the remainder of this guide.

### Everybody
Install AWS CLI by typing the following in your terminal:

    pip install awscli

**If on a Mac** type this instead:

    sudo pip install awscli --ignore-installed six

If you run into trouble with the installation, please consult [this site](http://docs.aws.amazon.com/cli/latest/userguide/installing.html#install-with-pip).

### Configuration 
**(Get your Access Key ID and Secret Access Key from us first)**

Once installed we need to configure it. We have created a user for you with the privileges to get information about your server. You have recieved your `<Access_ID>` and `<Secret_Key>` separately. In your terminal

    aws configure
    AWS Access Key ID []: <Access_ID>
    AWS Secret Access Key []: <Secret_Key>
    Default region name []: eu-west-1

Leave the rest as their default (just press enter).

You will also need the E2C Secret Key, a `.pem` file. Download it from [here](https://drive.google.com/open?id=0BxQkKe29iv9SUDcyZ0hsTWZmSTg), and save it somewhere that is easy to access. You will need to use this file everytime you want to launch your server.

Set the permissions using your terminal (if you are unsure how to `cd` using a terminal, please consult [this](http://askubuntu.com/questions/520778/how-can-i-change-directories-in-the-terminal)):

    cd <path to .pem file>
    chmod 400 aws-norbis-key.pem

## Workflow
You will be provided an Instance ID. This is the ID of your server for the entirity of the course. It will look something like this:

    i-0123456789abcdefg

Get your public DNS (for connecting to the server), and your public IP (for accessing the material in a browser) addresses. **These change value every time the server is shut down**.

    aws ec2 describe-instances --instance-ids <AWS instance ID> | grep Public

This will print the `publicDNS` and `publicIP` (three times). Save them somewhere temporarily.

Now we will connect to the actual server through `ssh`.

    cd <path to .pem file>
    ssh -i aws-norbis-key.pem icarus@<publicDNS>
    Are you sure you want to continue connecting (yes/no)? yes
    icarus@<...>'s password: changetheworld
    
We are now connected to the server. If you wish to disconnect from the server, and get you Bash terminal back type `exit`. Start the exercises by:

    cd 02456-deep-learning/
    jupyter notebook

We have now started a jupyter server, which is hosted via the AWS server. View the exercises by opening your browser, and navigate to

    https://<publicIP>:8888

If you have multiple instances of Jupyter running (or other processes using your ports) the `8888` might be a different number. Check the output in the terminal if the above doens't work.
We have a self-signed certificate, so your browser will most likely come with a security complaint. Click 'advanced' (or something similar) and click 'proceed'.

The password is `42`.

You should now see an overview of the course material. Enjoy!

## After the course

The servers that you used will be deleted. 
If you wish to keep using the material from the course ther are several options:

**Amazon Web Services**

The privileges you were granted have been revoked (the things you setup through `aws config`). 
If you want to continue working with AWS sign up, and create your own account [here](https://aws.amazon.com/). You need to get permission to use `p2.xlarge` servers. [Click here](http://aws.amazon.com/contact-us/ec2-request) to request this.


If you would like to continue working with AWS the AMI used for this course is made public, so you can recreate it if you so desire. You will need to create you own AWS account, select `Irland` as your regions, and when you `Launch Instance`, you should click `Community AMIs`. Search for `ami-b991c7ca`. You will also need to reconfigure your AWS CLI, by typing `aws configure`, and enter your own credentials. There are many AMI made available with a lot of software pre-installed. E.g. the AMI used for this course is based on this [one](https://github.com/Miej/GoDeeper).

You will need to configure your `Security Group`. These settings are simple and work, but don't really account for security concerns. For this course the following has been used:

![](security_group.png)

The `Custom TCP Rules` are necessary for hosting Jupyter Notebooks over the internet.

**Exercises and course material**

If you want to keep the exercises, and the work that you did 

    scp -i aws-norbis-key.pem -r icarus@<publicDNS>:~/02456-deep-learning/ <destination folder>

set `<destination folder` to `.` if you want to download it to the current folder

**Software setup**

Getting all the necessary software working on your own computer can be cumbersome. If you are on Linux or Mac it should be possible. Tensorflow on GPU doesn't work on Windows at time of writing. The key software that we use is:

 * Python 2.7 
   * (3.5 works for most things, not always on Windows though)
   * (We suggest Anaconda)
 * Theano v0.9.0.dev2
   * (The newest version, dev4, breaks some of the exercises. If you are using Anaconda you can get it from [here](https://anaconda.org/jaikumarm/theano))
 * Lasagne - newest
 * TensorFlow - newest
 * Cuda 7.5
   * (Installing TensorFlow for 8.0 is better, but harder at the moment)
 * cuDNN 5.0
   * (TensorFlow has the same issue with cuDNN 5.1)
