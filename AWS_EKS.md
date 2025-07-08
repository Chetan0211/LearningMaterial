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
eksctl craete nodegroup --help
```