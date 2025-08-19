--------------->
kubectl installation
--------------->   			https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html


curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install


--------------->
kubectl installation
--------------->   			https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
   
sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
    
  # If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg # allow unprivileged APT programs to read this keyring

# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list   # helps tools such as command-not-found to work correctly

sudo apt-get update
sudo apt-get install -y kubectl



---------------->
eksctl installation
--------------->   			https://eksctl.io/installation/


# for ARM systems, set ARCH to: `arm64`, `armv6` or `armv7`
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"

# (Optional) Verify checksum
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz

sudo install -m 0755 /tmp/eksctl /usr/local/bin && rm /tmp/eksctl


---------------->
helm installation
--------------->   			https://helm.sh/docs/intro/install/


curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm





aws configure 





eksctl create cluster \
  --name my-cluster \
  --region ap-south-1 \
  --version 1.33 


  
eksctl create nodegroup \
  --cluster my-cluster \
  --name my-nodes \
  --nodes 3 \
  --nodes-min 2 \
  --nodes-max 6 \
  --node-type t3.medium
 
 
 
aws eks update-kubeconfig --name my-cluster --region ap-south-1
  


eksctl utils associate-iam-oidc-provider --cluster my-cluster --approve


	link for new updated policy----->	https://docs.aws.amazon.com/eks/latest/userguide/lbc-manifest.html

curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.13.3/docs/install/iam_policy.json

aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
  
  
  eksctl create iamserviceaccount \
  --cluster=my-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::<1111111112222 - your aws account id >:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --region ap-south-1 \
  --approve
  
  
  
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks

helm list -A
helm search repo eks/aws-load-balancer-controller --versions


helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=my-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=ap-south-1
  --version 1.13.0
  
  
Optional: List available versions of the chart

helm search repo eks/aws-load-balancer-controller --versions
Verify that the controller is installed:

kubectl get deployment -n kube-system aws-load-balancer-controller



Apply the namespace:

kubectl apply -f 01-ns.yaml
Set it as the default for the current context to avoid specifying -n app1-ns repeatedly:

kubectl config set-context --current --namespace=store-ns



eksctl delete cluster --name my-cluster1 --region ap-south-1

