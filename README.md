IaaS, PaaS.

# IaaS

Infrastructure as a service.

A computer that you can rent, SSH into, and do anything you want.

Often come with extra tools to manage databases, notifications, email sending, etc.

## AWS

Amazon Web Services.

Must use leave credit card info to use.

Free service for one year only on some services.

If you use any service that is not included, or go over the limit of any service, you pay. There is no way to limit the usage to free only.

It is however possible to set a billing WatchCloud alarm that notifies you if the estimated cost goes over a threshold, which you can then set at US 0.01.

AWS allows to install one image on a virtual machine called an instance.

AWS images have an specific format called IAM.

Amazon provides Ubuntu, RHEL, Windows and other IAMs.

There are also public third party IAMs that you can install, typically implementing one entire web app stack.

Bitnami has many of those, and they can be accessed at AWS at instance creation

As of 2013, free time for an instance is one instance running a full month, therefore much less than Heroku's free 5 instances per month.

To SSH into an Ubuntu session:

    ssh ubuntu@54.194.35.131

Make sure that ssh is allowed by the security groups!

### Security groups

All traffic is forbidden by default.

To allow new types of TCP traffic you must add security groups to the instance.

### Static IP

By default, every time you stop / start an application its IP and DNS change.

To avoid that, AWS uses so called Elastic IPs EIPs.

They are fixed, each account can have 5 for free.

If you don't associate an EIP to an instance you pay US 3.6 / MiB! Therefore make sure that you associate every EIP you create immediately to an existing instance.

### S3

Simple Storage Server.

Store files.

Can serve static websites.

You must configure the buckets before:

- Bucket > Properties > Static website hosting. Select a file to be the index at the root, usually `index.html`.
- Right click on files which should be public > Make public

Directory upload from the web interface requires enabling the beta Java uploader.

Websites will be public at:

    http://<bucketname>.s3-website-<zone>.amazonaws.com

##Digital Ocean

No free tier.

Starts at 5$/mo for:

- 512MB Memory
- 1 Core Processor
- 20GB SSD Disk
- 1TB Transfer

2c per GB transfer.

<https://www.digitalocean.com/pricing/>

##Google Compute Engine

Quickstart:

Create account, project and instance.

Install GCE and setup instance:

    sudo apt-get install curl
    curl https://dl.google.com/dl/cloudsdk/release/install_google_cloud_sdk.bash | bash
    gcloud auth login
    getproject --project="PROJECT_NAME" --cache_flag_values

Login SSH, get source, compile and run:

    gcutil --service_version="v1" --project="PROJECT_NAME" ssh --zone="europe-west1-b" "INSTANCE_NAME"
    sudo apt-get update
    sudo apt-get install git
    git clone --recursive https://github.com/google-hash-code-paris-2014-tmo/cpp-template
    cd cpp-template
    ./configure
    make run

Stop instance, don't get charged, and keep the disk: <http://stackoverflow.com/questions/20153695/how-to-stop-compute-engine-instance-without-terminating-the-instance>

##OpenStack

Open source IaaS with many backing companies.

<http://thoughtsoncloud.com/2013/07/are-cloud-foundry-and-openstack-compatible/>

#PaaS

Platform as a Service.

Also a computer that you can rent, but with:

- less flexibility
- more things ready out of the box

when compared to IaaS.

PaaS services have a limited list of supported services, typically web frameworks like Python, Rails, but there are also other types of services.

Since they are more restricted, PaaS services can be cheaper.

##Cloud Foundry

<http://cloudfoundry.org>

##OpenShift

OpenShift runs on open source software by Red Hat.

It offers ssh server to a Red Hat Enterprise Linux Server system with the following limitations:

- the ports you can bind to
- no sudo, because all apps run on a single system.

The only way then to install new software is by either making `yum` install things locally, or `wget + make + make install` on a local dir + path manipulation. Good luck.

Free plans gives you:

- 3 apps per email
- 1 GB HD each
