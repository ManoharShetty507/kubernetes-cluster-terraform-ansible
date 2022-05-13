# kubernetes-cluster-terraform-ansible
Build Kubernetes Cluster on AWS using Terraform and Ansible


Requirements:
------------
&#8594; AWS Account
- Create Redhat EC2 Instance
- Create IAM role with required policies
  - VPC full-access
  - EC2 full-access
- Attach IAM Role to EC2 Instance

Create a User:
--------------
-  sudo useradd ansible
-  echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
-  sudo su ansible

Install Terraform
------------------
- sudo su ansible
- sudo yum install wget unzip -y
- wget https://releases.hashicorp.com/terraform/0.12.28/terraform_0.12.28_linux_amd64.zip
- sudo unzip terraform_0.12.28_linux_amd64.zip -d /usr/local/bin
- export PATH=$PATH:/usr/local/bin
- vi .bashrc
&#8594; Add this content
- export PATH="$PATH:/usr/local/bin"
- source ~/.bashrc

Ansible Installation
--------------------
- sudo su ansible
- sudo yum install python3 -y
- sudo alternatives --set python /usr/bin/python3
- sudo yum -y install python3-pip -y
- pip3 install ansible --user
- pip3 install boto3 --user

Initiate & Setup the Cluster using Terraform
-------------------------------------

- terraform init 
- terraform validate
- terraform plan
- terraform apply
- or &#8595;
- terraform apply --auto-approve 

Configuration Management using Ansible
--------------------------------------
&#8594; We will using AWS SDK Boto3 to write a python script to Servers based on Tag name and Value.

&#8594; Check if all the hosts are pinging
- ansible all -m ping
- Let's set the default region
- * mkdir ~/.aws

-  vi ~/.aws/config
&#8594; Add this lines
    - [default]
    - region=ap-south-1

- chmod +x DynamicInventory.py 
- ./DynamicInventory.py --list

&#8594; Copy the Pem File
- vi ~/k8-allinone.pem

&#8594; Change the permission of the key
- chmod 400 k8-new-key-pair.pem

&#8594; Check if there is interconnectivity among the servers
- ansible Kubernetes_Servers -i DynamicInventory.py -m ping --ssh-common-args="-o StrictHostKeyChecking=no" -u ubuntu --private-key=./k8-new-key-pair.pem

Run the Play Book
-------------------

- ansible-playbook -i DynamicInventory.py site.yml -u ubuntu --private-key=./k8-new-key-pair.pem

Destroy the Cluster
-------------------
- terraform destroy
- (or) &#8595;
-  terraform destroy --auto-approve
