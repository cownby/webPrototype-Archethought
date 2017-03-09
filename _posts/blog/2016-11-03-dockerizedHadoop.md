---
layout: post
title: "Prototype Hadoop Cluster"
author: carolyn_ownby
share: true
modified:
categories: blog
tags:	[docker, hadoop]
date:   2016-11-03T12:00:00

---

# Overview

Want to play with hadoop without a lot of overhead? This is a step-by-step guide to set up a prototype hadoop cluster, load data, and access that data from python.

# Create Hadoop Cluster

Create a temporary Hadoop cluster as proof of concept using 
[KiwenLau's implementation](http://kiwenlau.com/2016/06/26/hadoop-cluster-docker-update-english/).

First install Docker if necessary: [Installation instructions here](https://docs.docker.com/engine/installation/linux/ubuntulinux/),
or start the daemon: `sudo service docker start`
Follow directions on KiwenLau's page:

~~~
1. sudo docker pull kiwenlau/hadoop:1.0
2. git clone https://github.com/kiwenlau/hadoop-cluster-docker
3. sudo docker network create --driver=bridge Hadoop
4. cd hadoop-cluster-docker
5. sudo ./start-container.sh
~~~

This will leave you inside the container as user root, in directory /root.

## Grant access to Hadoop Master

The container already has ssh, and turns out that explicitly opening port 22 is unnecessary and in fact 
errors with a conflict. Root is the only user defined within the container, and you must add 
authentication keys for whoever will log into the container.

Append the contents of the users public key (~/.ssh/id_rsa.pub) to the container's root 
~/.ssh/authorized_keys file. Example:
 
~~~
ssh-copy-id my-user-name@host-machine-ip-address
~~~

Now, if you exit the container and need to get back in, you can gain access with ssh; for example: `ssh root@172.18.0.4`

## Start Hadoop

Start up Hadoop from within the master hadoop container. Again, per [Kiwen Lau's site](http://kiwenlau.com/2016/06/26/hadoop-cluster-docker-update-english/):

~~~bash
./start-hadoop.sh
~~~

And test: 

~~~bash
 ./run-wordcount.sh
~~~

See [Kiwen Lau's website](http://kiwenlau.com/2016/06/26/hadoop-cluster-docker-update-english/) for expected output.

## Load the Data

Add your data file to the container, and then put it into Hadoop.

Example using sftp from the container host to the container:

~~~
sftp root@<container ip address>
put <data files(s)>
exit
~~~

or, from within the container:

~~~
sftp <host user>@<host ip> 
get  <data files(s)>
exit
~~~

Create an input directory on HDFS, if desired:
`hadoop fs -mkdir -p <directory name>`.
Example: 
`hadoop fs -mkdir -p testData`

Add the file(s) to HDFS

~~~bash
hdfs dfs -put <data files(s)> <directory name>
ex: hdfs dfs -put simpleSample.csv testData
~~~

Look to ensure the data is really there!

~~~
hdfs dfs -ls
~~~

See what else you can do with the hadoop file system with `hdfs --help`.

You can now exit the Hadoop master container.

# Access the data 

There are several python libraries for hadoop hdfs access. This example uses Spark 1.6 and
[snakebite](https://snakebite.readthedocs.io/en/latest/index.html).

Here is a Python snippet that reads a file from HDFS and prints each line for testing purposes. 
(Don't do this with a large file!)
This assumes the Hadoop master docker container ip address is "172.18.0.4", 
our data file is "simpleSample.csv", 
and it is located in directory "testData"

~~~python
from snakebite.client import Client
datafile = r'/user/root/testData/simpleSample.csv'
client = Client("172.18.0.4", 9000 , use_trash=False, 
effective_user="root")
f = client.text([datafile])
for line in f:
        print "line from file: " ,line
~~~

# You might want to know:

## Access Port

9000 is a standard HDFS access port number, and the one that Hadoop-master uses.

## Accessing a docker container

Once you have set up ssh authentication, you may gain access with ssh; for example: `ssh root@172.18.0.4`.
Otherwise, issue the command `docker exec -it <container name> bash`

## Container IP Address

To determine a docker container's IP address, enter the container and issue an ifconfig command. The eth0 IP address is that of the container.

~~~bash
$ docker exec -it <container name> bash
$ ifconfig
~~~

Or externally with: 

~~~bash
$ docker inspect <container name>
~~~



	

