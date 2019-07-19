# PART1 - 빅데이터 클러스터 구축 (07/19)

### 환경구성
- CENTOS 7
- JDK 1.8
- CDH 5.15

## 1. create a CDH Cluster on AWS
#### 1) linux setup
List the your instances by IP address and DNS name 
![ui1](/images/hosts.png)

List the Linux release you are using
![ui1](/images/linux.png)

List the file system capacity for the first node
![ui1](/images/du.png)

List the command and output for yum repolist enabled
![ui1](/images/repolist.png)

List the /etc/passwd entries for training
![ui1](/images/passwd.png)

List the /etc/group entries for skcc
![ui1](/images/usradd.png)

List output of the flowing commands:
1. getent group skcc
2. getent passwd training
![ui1](/images/getent.png)


#### 2) Install a MySQl server

A command and output that shows the hostname of your database server
![ui1](/images/db_hostname.png)

A command and output that reports the database server version
![ui1](/images/db_version.png)

A command and output that lists all the databases in the server
![ui1](/images/db_db.png)

#### 3) Install Cloudera Manager

Make sure that the following services (and any necessary services to install that service) are installed
![ui1](/images/1_1.png)

![ui1](/images/1_2.png)

In you cluster, create a user named “training” with password “training”
![ui1](/images/training_hdfs.png)


## 2. In MySQL create the sample tables that will be used for the rest of the test

In MySQL, create a database and name it “test”
![ui1](/images/db_test.png)

Create 2 tables in the test databases: authors and posts.
![ui1](/images/posts.png)

![ui1](/images/authors.png)

Create and grant user “training” with password “training” full access to the test database. 
![ui1](/images/user.png)


