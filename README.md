✅ What is KOPS.

Kops = Kubernetes Operations Tool
Kops is required because it automatically creates a complete, production-ready Kubernetes cluster on AWS — saving huge time, effort, and mistakes that come with manual setup.
It automatically creates a Kubernetes cluster on AWS (EC2, VPC, Subnets, Route53, Autoscaling groups, etc.).
Instead of doing everything manually → Kops does it in 1 command.



✅ Why KOPS is Needed.

Running Kubernetes manually on AWS is very difficult.
Here you need to create everything yourself like VPC, Subnets, RouteTables, Security Groups, EC2 instances(master + workers), IAM roles,
Load Balancers, Networking etc…

Doing all this manually takes hours and is very error-prone.
So Kops makes this entire process automatic.







✅ KOPS-KUBERNETES CLUSTER SETUP.


✅ Requirements.

Linux machine (Ubuntu) → where you run commands
AWS Account → Kops uses EC2, VPC, S3, IAM
kops tool → creates Kubernetes cluster
kubectl → used to talk to Kubernetes cluster



✅ STEP 1 — Prepare Ubuntu Server

#Update your system:
sudo apt-get update -y

#Install unzip:
sudo apt install unzip -y



✅ STEP 2 — Install AWS CLI (so Kops can talk to AWS)

#Download AWS CLI:
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

#Unzip it:
unzip awscliv2.zip

#Install:
sudo ./aws/install

✅ Now your Linux machine can run AWS commands.




✅ STEP 3 — Install Kops (main tool)
1. Download Kops:
curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s \
https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

2. Make it executable:
chmod +x ./kops

3. Move it into PATH:
sudo mv ./kops /usr/local/bin/

✅ Now you can type kops anywhere.




✅ STEP 4 — Install kubectl
1. Download kubectl:
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s \
https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

2. Make it executable:
chmod +x ./kubectl

3. Move it into PATH:
sudo mv ./kubectl /usr/local/bin/kubectl

✅ kubectl = used to check pods, deployments, services, etc.




✅ STEP 5 — Create IAM User for Kops

Kops needs access to AWS services.
So create an IAM user in AWS with these permissions:

✅ AmazonEC2FullAccess
✅ AmazonRoute53FullAccess
✅ AmazonS3FullAccess
✅ IAMFullAccess
✅ AmazonVPCFullAccess

You can use AdminAccess for learning/testing.




✅ STEP 6 — Configure AWS CLI on your Linux machine

#Run:
aws configure

Provide the IAM user’s:
✅ Access key
✅ Secret key
✅ Default region
✅ Output format

#Check users:
aws iam list-users


#Export creds:
export AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id)
export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)

✅ Now your machine can authenticate to AWS.



✅ STEP 7 - Create a s3 bucket

 go to s3 in management console and create an empty s3 bucket
 bucketname : ex-kopscluster123



✅ STEP 8 — Prepare Environment Variables

#Set cluster name:
export NAME=venkat.k8s.local


#Set S3 bucket (Kops stores cluster config here):
export KOPS_STATE_STORE=s3://bucket-name

✅ You MUST create this S3 bucket beforehand.



✅ STEP 9 — Create a Kubernetes Cluster using Kops

#Single-zone cluster:
kops create cluster --zones ap-south-1a ${NAME}

✅ Kops prepares all AWS resources:

VPC
Subnets
EC2 Master + Worker nodes
Route53 DNS
Security groups
IAM roles

Nothing is created yet — only configuration.



✅ STEP 10 — Build/Start the cluster
kops update cluster --name ${NAME} --yes

(You didn’t include it, but this step is required for creation)
✅ This will actually create resources in AWS.



✅ STEP 11 — Check Cluster Status

kops get cluster

Validate cluster:
kops validate cluster

✅ It will tell you if the control plane & nodes are healthy.




✅ STEP 12 — Delete Cluster (Cleanup)
kops delete cluster --name=venkat.k8s.local --state=s3://bucket-name --yes

✅ Removes EC2 instances, VPC, networking, everything.



✅ Simple Summary...

Install AWS CLI	So Kops can talk to AWS
Install Kops	Tool that creates Kubernetes cluster
Install kubectl	Command-line tool for Kubernetes
Create IAM User	Give permissions to Kops
Configure AWS CLI	Provide IAM access keys
Set NAME & S3	Store cluster config in S3
kops create cluster	Create cluster configuration
kops update cluster	Actually build cluster in AWS
kops validate cluster	Check cluster health
kops delete cluster	Destroy cluster
