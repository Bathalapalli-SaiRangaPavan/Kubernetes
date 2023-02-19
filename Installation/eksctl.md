# EKS-Create-Cluster-using-eksctl in Amazon-Linux

### Step 1 - Install - AWS CLI 

```
aws --version
```
 - By default you can able to see aws cli as we r using amazon linux ```aws-cli/2.10.0 Python/3.9.11 Linux/5.10.165-143.735.amzn2.x86_64 exe/x86_64.amzn.2 prompt/off```

### Step 2 - Set Up kubectl

-  Reference - https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html

##### Install Kubernetes 1.23

```
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.23.16/2023-01-30/bin/linux/amd64/kubectl
```
Execution Permissions
```
chmod +x ./kubectl
```
Move kubectl to Usr local bin
```
sudo mv ./kubectl /usr/local/bin
```
Check the kubectl version 
```
kubectl version --short --client
```

### Step 3 - Set Up eksctl

- Reference - https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
```
```
sudo mv /tmp/eksctl /usr/local/bin
```
```
eksctl version
```
### Step 4 - Create an IAM Role and attach it to EC2 instance

- Go to AWS Management Console 
- Click (Services) 
- Search (IAM) 
- Click (Roles)
- Click (Create role)
##### Select trusted entity
- Select (Aws Service) 
##### Use case 
- Select (EC2) # we are attaching to ec2 
- Click (Next) 
##### Add permissions
##### Search 
- Filter Policies (IAM Full Access) Select (IAM Full Access) (Provides full access to IAM via the AWS Management Console.)
- Filter Policies (Cloud Formation) Select (AWSCloudFormationFullAccess) (Provides full access to AWS CloudFormation.)
- Filter Policies (VPC) Select (AmazonVPCFullAccess) (Provides full access to Amazon VPC via the AWS Management Console.)
- Filter Policies (EC2) Select (AmazonEC2FullAccess) (Provides full access to Amazon EC2 via the AWS Management Console.)
- Filter Policies (Administrator) Select (AdministratorAccess) (Provides full access to AWS services and resources.)
- Click (Next)
##### Role name
- Enter a meaningful name to identify this role (bootstrap_role)
- Click (Create Role)

### Now Attach It To Ec2 Instance

- Go to AWS Management Console 
- Click (Services) 
- Search (EC2) 
- Select (Server) # Where we installed awscli, kubectl
- Select (Actions) 
- Click (Security) 
- Click (Modify IAM Role) 
- Select (bootstrap_role) 
- Click (Update IAM role)


### Step 5 - Create a cluster 
- Note - I given name as mycluster. You can give any name.
#####  Execute below command 

```
eksctl create cluster --name mycluster \
                      --region us-east-1 \
                      --node-type t2.small \
```
###### It will take 15 minutes time to execute. 
###### Meanwhile if u go to AWS Management Console -> Services -> CloudFormation  (u can see # eksctl-mycluster-cluster - CREATE_IN_PROGRESS)
###### Once its executed successfully u can able to see (eksctl-mycluster-cluster - CREATE_COMPLETE)
###### go to AWS Management Console -> Services -> Elastic Kubernetes Service -> you can see (mycluster)  
###### new instances with t2.small is created 

Validate cluster by checking the nodes
```
kubectl get nodes
```
Create a sample pod 

```
kubectl create -f nginx-pod.yaml
```

```
kubectl apply -f nginx-pod.yaml
```

```
kubectl get pods
```
#### Delete the cluster 

```
eksctl delete cluster mycluster --region us-east-1
```

