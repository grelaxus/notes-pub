
# Configure credentials
When aws CLI is used through docker container (or other situations where AWS security creds need to be specified), configure credentials such that they are not exposed in command line.  
One way of doing that is to specify them in a separate file and then source it and export. E.g.  

~/.aws/qqq:
```
AWS_ACCESS_KEY_ID=AKIAI....KEY_ID....
AWS_SECRET_ACCESS_KEY=pog...secret...xmcnvher
AWS_DEFAULT_REGION=us-east-1
```
~/.aws/config.sh:
```sh
!#/bin/bash
file=$1
source $file
export $(cut -d= -f1 $file)
```
And then just call it before running aws cli:  
```sh
source ~/.aws/config.sh ~/.aws/qqq
```
PS: it needs to be sourced so that variables are accessible in current shell (otherwise they are available in a subshell, see more details [here](https://stackoverflow.com/questions/10781824/export-not-working-in-my-shell-script))

# Metrics
300K hits per day is said to be possible in t1.micro (see comments):  
https://www.quora.com/Can-I-use-an-AWS-micro-instance-to-effectively-run-a-small-web-server-using-nginx

Rough calculations for t2.micro:  
https://www.quora.com/Can-I-use-the-t2-micro-EC2-instance-to-handle-around-2000-requests-on-my-web-server-Apache2-MySQL-PHP

A **t2.micro** instance comes with 1 GB, from which you can utilize around 920 Mb, arnd 100mb get consumed by other system process. 
If a single user connection takes around 20MB of memory, 
then **parallely** you can server 920/20 = **46 users**. That means around 46 users can give test in parallel. 
However if users are not concurrent(parallel) then you can server thousands of users. 
Based on my experiences I can say you cannot server 2000 users concurrently on T2 micro. However if concurrency is 10–15, You can easily serve.


t2.nano observation:  
https://www.concurrencylabs.com/blog/handle-thousands-of-users-with-a-t2-nano/
