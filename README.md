# Autoscaling Wordpress with Ansible and CloudFormation

[http://allandenot.com/devops/2015/12/24/autoscaling-wordpress-with-ansible-and-cloudformation.html](http://allandenot.com/devops/2015/12/24/autoscaling-wordpress-with-ansible-and-cloudformation.html)

## The Goal

- A WordPress site running in EC2 hosts and an RDS database.
- EC2 hosts inside an autoscaling group
- Easily deploy configuration changes

## How it works

User runs Ansible playbook:

![blog-ansible-autoscaling-wordpress.png](https://allandenot.com/images/blog-ansible-autoscaling-wordpress.png)

Notes:

- Baking server exists as a template for the AMI that will be used in CloudFormation.
- Baking server is an Ubuntu with Ansible and local playbooks are copied into it.
- We choose to use baked AMIs to speedup the bootup process when autoscaling is creating new instances. Upon boot, these instances run their local Ansible playbooks and the webserver is configured.

## How can I use it

Fork my repository: [https://github.com/adenot/blog-ansible-autoscaling-wordpress](https://github.com/adenot/blog-ansible-autoscaling-wordpress)

Edit file:

    group_vars/all
    
Set variables from your EC2 account.

Running:

    ansible-playbook -vv stack-wordpress.yml
    
### What is happening?

First time it runs:

- a baking server is created
- local playbooks are copied into baking server (from local_wordpress/ansible)
- an AMI is created from the baking server
- a CloudFormation stack is created

Second time it runs:

- local playbooks are copied into baking server (from local_wordpress/ansible)
- an AMI is created from the baking server
- CloudFormation stack is updated with new AMI
- Autoscaling group replaces EC2 instances with new ones using new AMI, one by one
