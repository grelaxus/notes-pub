
# Expand root partition on running EC2 instance

1. Increase the size in the AWS console:
   https://blog.shikisoft.com/increase-root-volume-of-ebs-backed-ec2/

2. Follow [these](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html?icmpid=docs_ec2_console) steps to extend the root partition on the Linux. The key commands are:
```sh
[ec2-user ~]$ df -hT
[ec2-user ~]$ lsblk
[ec2-user ~]$ sudo growpart /dev/nvme0n1 1
[ec2-user ~]$ sudo xfs_growfs -d /
```
 

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

# Useful commands
## awc cli
### Run instances
Run EC2 instance (e.g. ami-efa8f88f) with a block device mapping (specified in the config.json) with public IP, subnet, and tag node_type=storage.
Note: to have the instance available publicly --associate-public-ip-address is not enough, DNS names need to be enabled in VPC (check [this](https://forums.aws.amazon.com/thread.jspa?threadID=316395) and [this](https://www.edureka.co/community/12614/ec2-instance-has-no-public-dns) out) and Internet GW needs to be added (as well as inbound and outbound rules need to be added in Security group)
```sh
aws ec2 run-instances --image-id ami-efa8f88f --instance-type t2.medium --block-device-mappings file://$(pwd)/../ebs-config.json --key-name myKeyPair --associate-public-ip-address --region us-west-1 --subnet-id subnet-0b9d48bbca2dc7aa7 --tag-specifications 'ResourceType=instance,Tags=[{Key=node_type,Value=storage}]'
```
the ebs-config.json looks like this:
```json
[
  {
    "DeviceName": "/dev/xvdb",
    "Ebs": {
    "DeleteOnTermination": true,
    "VolumeType": "gp2",
    "VolumeSize": 30
    }
  }
]
```
After running the instances we can list their public names filtering by the tag key (i.e. "node_type")
```sh
aws ec2 describe-instances --region us-west-1 --filters "Name=tag-key,Values=node_type" --query 'Reservations[*].Instances[*].PublicDnsName' --output text
```
OR tag key/value (i.e. "storage")
```sh
aws ec2 describe-instances --region us-west-1 --filters "Name=tag-key,Values=node_type" --filters "Name=tag-value,Values=monitor" --query 'Reservations[*].Instances[*].PublicDnsName' --output text
```
If the outpud is preheaded with empty line, those can be cleaned up:
```sh
aws ec2 describe-instances --region us-west-1 --filters "Name=tag-key,Values=node_type" --filters "Name=tag-value,Values=monitor" --query 'Reservations[*].Instances[*].PublicDnsName' --output text| sed '/^$/d'
```
This same describe-instances command can be used to ssh-edit files. In the following example I get the list of the hostnames from each EC2 host:
```sh
for i in `aws ec2 describe-instances --region us-west-1 --filters "Name=tag-key,Values=node_type" --query 'Reservations[*].Instances[*].PublicDnsName' --output text`; \ 
do ssh -i cephKeyPair.pem fedora@$i.us-west-1.compute.amazonaws.com "sudo hostname"; done
```
And here I edit the /etc/hosts with those hosts (the command can be improved obviously):
```sh
for i in `aws ec2 describe-instances --region us-west-1 --filters "Name=tag-key,Values=node_type" --query 'Reservations[*].Instances[*].PublicDnsName' --output text`; \
do ssh -i myKeyPair.pem fedora@$i 'echo "10.0.16.206 ip-10-0-16-206.us-west-1.compute.internal" | sudo tee -a /etc/hosts; \
echo "10.0.16.206 ip-10-0-16-206.us-west-1.compute.internal" | sudo tee -a /etc/hosts; \
echo "10.0.20.204 ip-10-0-20-204.us-west-1.compute.internal" | sudo tee -a /etc/hosts; \
echo "10.0.26.27 ip-10-0-26-27.us-west-1.compute.internal" | sudo tee -a /etc/hosts; \
echo "10.0.22.24 ip-10-0-22-24.us-west-1.compute.internal" | sudo tee -a /etc/hosts; \
echo "10.0.26.223 ip-10-0-26-223.us-west-1.compute.internal" | sudo tee -a /etc/hosts; \
echo "10.0.28.196 ip-10-0-28-196.us-west-1.compute.internal" | sudo tee -a /etc/hosts'; done
```

Similarly other fields also can be queried. To get a json output omit the '--output text' option 

