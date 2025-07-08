# AWS EKS Cheat sheet

(Insalling steps are provided for MAC OS, for other OS refer online)
Install AWS CLI into the system using following commands.

```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
```

After downloading we need to install it using below command
```bash
sudo installer -pkg ./AWSCLIV2.pkg -target /
```

```bash
aws --version #This will provide the version of AWS CLI
```

After that we need to configure AWS CLI. To do that we need to use below command

```bash
aws configure
```

It will ask for "Access key ID", "Secret access key", "Default region name"(Select the default region, ex: us-east-1) and "Default output format"(This we keep as JSON).

You can get access key and secrect access key in IAM under user.

You can check if the config is working by using
```bash
aws ec2 describe-vpcs
```

For installing kubectl I've followed official site [Official KS8 website](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/).


You also need eksctl CLI in your system. Install it using brew

```bash
brew tap weaveworks/tap

brew install weaveworks/tap/eksctl

eksctl version
```

Once we have eksctl command line tool in our system we can create new clusters from our console.

#### Create cluster without nodegroup
```bash
eksctl create cluster --name=eksdemo\
                      --region=us-east-1\
                      --zones=us-east-1a,us-east-1b,us-east-1c\
                      --without-nodegroup 
```
We didn't created nodegroup here because we will be doing it seperately.
(It takes 15 to 20 mins to create a cluster.)

Once created we should able to see all the nodes using 
```bash
kubectl get nodes # This will not show any nodes as we didn't created any nodegroup.
```
(If this dosen't work check while creatig cluster is has saved in kubecofig or not. If Not search in net how to do that.)

```bash
ekctl get clusters # Used to get the clusters
```


To create and associate **IAM OIDC Provider** for out EKS cluster we need to execute following command:

```bash
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster eksdemo \
    --approve
```

#### Creation of nodegroup using Add Ons

For creating nodegroups you can get help on what are the things you can use while creating nodegroups by using
```bash
eksctl create nodegroup --help
```

```bash
#To create nodegroup for a cluster
eksctl create nodegroup --cluster=eksdemo \ # cluster name to which nodegroup has to be created
                       --region=us-east-1 \ # Region where this nodegroup has to be created
                       --name=eksdemo1-ng-public1 \ # Name for the nodegroup
                       --node-type=t3.medium \ # Type of instance 
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \ # Each node has 20GB of size 
                       --ssh-access \ # We are setting ssh access to true
                       --ssh-public-key=kube-demo \ # we are saying to use kude-demo as ssh key which was created earlier in our EC2 key pair
                       --managed \ # AWS is managed automatically like updating, patching etc.
                       --asg-access \ # Auto scaling group access(reate IAM policy in worker IAM role)
                       --external-dns-access \ # External DNS access(Route 53 DNS should be registered and tha trelated IAM policy is created)
                       --full-ecr-access \ # Enable full access to ECR(Elastic Container Registry) IAM policy is created for this
                       #sameway we are giving permissions to below once too
                       --appmesh-access \
                       --alb-ingress-access 
```

Now agin you can check the nodes created using `kubectl get nodes`

To get a more info on node you can use
```bash
kubectl get nodes -o wide
```