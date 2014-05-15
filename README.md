
# AWS

Must use leave credit card info to use.

Free service for one year only on some services.

If you use any service that is not included, or go over the limit of any service, you pay. There is no way to limit the usage to free only.

It is however possible to set a billing WatchCloud alarm that notifies you if the estimated cost goes over a threshold, which you can then set at US 0.01.

AWS allows to install one image on a virtual machine called an instance.

AWS images have an specific format called IAM

Amazon provides Ubuntu, RHEL, Windows and other IAMs.

There are also public third party IAMs that you can install, typically implementing one entire web app stack.

Bitnami has many of those, and they can be accessed at AWS at instance creation

As of 2013, free time for an instance is one instance running a full month, therefore much less than Heroku's free 5 instances per month.

To SSH into an ubuntu session:

    ssh ubuntu@54.194.35.131

Make sure that ssh is allowed by the security groups!

## Security groups

All traffic is forbidden by default.

To allow new types of TCP traffic you must add security groups to the instance.

## Static ip

By default, every time you stop / start an application its IP and DNS change.

To avoid that, AWS uses so called Elastic IPs EIPs.

They are fixed, each account can have 5 for free.

If you don't associate an EIP to an instance you pay US 3.6 / mo!
Therefore make sure that you associate every EIP you create immediately to an existing instance.
