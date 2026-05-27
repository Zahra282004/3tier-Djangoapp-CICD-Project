# 1: Install and configure Jenkins on ec2 master

# 2: Create EKS Cluster on AWS (Master machine)
connect master node with aws
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws configure
```

# 3: Install kubectl (Master machine)(Setup kubectl )
```
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

# 4: Install eksctl (Master machine) (Setup eksctl)
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```
- Create EKS Cluster (just create control plan not whole node group)
```
eksctl create cluster --name=wanderlust \
                    --region=us-east-2 \
                    --version=1.30 \
                    --without-nodegroup
```

- Associate IAM OIDC Provider (Kubernetes Pod having access to other aws services)
```
eksctl utils associate-iam-oidc-provider \
  --region us-east-2 \
  --cluster wanderlust \
  --approve
```
- Create Nodegroup (Master machine)
```
eksctl create nodegroup --cluster=wanderlust \
                     --region=us-east-2 \
                     --name=wanderlust \
                     --node-type=t2.large \
                     --nodes=2 \
                     --nodes-min=2 \
                     --nodes-max=2 \
                     --node-volume-size=29 \
                     --ssh-access \
                     --ssh-public-key=eks-nodegroup-key
```
# 5: Install docker (image build)
# 6: Install and configure SonarQube (static code analysis)
# 7: Install Trivy(vulnerabilities and file system detection)
