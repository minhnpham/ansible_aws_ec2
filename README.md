# ansible_aws_ec2
## SUMMARY
This Ansible playbook performs the following:  
On AWS:-
1. Creates a VPC
2. Provisions the public/private subnets in the VPC
3. Defines the Security Groups for ssh/http/https
4. Provisions an Internet Gateway into the VPC
5. Defines routing tables for public access
6. Creates/Saves a EC2 key pair
7. Provisions a EC2 instance with CENTOS 7, with Security Group for ssh/http/https
8. Adds new EC2 instance host into Ansible in-memory inventory

On the EC2 instance:-
1. Install firewalld if not present
2. Configures firewalld to deny ALL apart from ssh/http/https
3. Install and Configure Docker CE
4. Pull/Run nginx container
5. Health Checks (inspect) to see if nginx container is running
6. Fetches the output of the nginx default http page
7. Create Word Frequency Test result to nginx results page
8. Setup cron job to monitor nginx container resource usage stats


# REQUIREMENTS
+ Ansible 2.5+
+ AWS User/IAM access key, with the following policy permissions:
  - AmazonVPCFullAccess
  - AmazonEC2FullAccess
  - AWSMarketplaceFullAccess (for Official Centos AMI access)


# NOTES:
+ This playbook has only been tested on AWS Region ap-southeast-2 (Sydney), for other regions please change to an appropriate AMI configuration in:
`./ansible_plays/roles/aws_provision_ec2/defaults/main.yml`


# Usage
1. Clone this repo. 
```
git clone ....
```

2. Setup your AWS Access keys, and region. e.g.
```
$ export AWS_DEFAULT_REGION=ap-southeast-2
$ export AWS_ACCESS_KEY_ID=<SomeKey>
$ export AWS_SECRET_ACCESS_KEY=<SomeSecret>
```

3. Run the Ansible playbook
```
$ ansible-playbook ansible_plays/site.yml
```

4. Note down the IP address of the EC2 instance during the playbook run. For documentation purposes lets call this _"ec2_instance_ip"_.

5. Open up a browser and point it to:
[http://ec2_instance_ip](http://ec2_instance_ip)  
You should now be seeing the default nginx html page.

5. Open up a browser and point it to:
[http://ec2_instance_ip/wordcountresults.html](http://ec2_instance_ip/wordcountresults.html)  
This page shows the word that occurs the most on the default nginx page (excluding html tags)

6. SSH into the EC2 instance to see the nginx docker container resource usage logs:
```
$ ssh -i ansible_plays/aws-ec2-key.pem centos@ec2_instance_ip
$ tail -f /var/log/docker_stats.log
```
Notes:
+ This log file updates every ~10 seconds.
+ The aws-ec2-key.pem file was generated as part of the ec2 provisioning automation.