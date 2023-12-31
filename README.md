# module-11
Kubernetes on AWS - EKS

Create EKS cluster with AWS management console
1. Created new role with AmazonEKSClusterPolicy named eks-cluster-role
2. Created VPC stack with template for private and public subnets. Range of IPs not changed
3. Created EKS Cluster, named "eks-cluster-test" and checked for correct role, i created before
4. Assigned VPC and security group i created before
5. set cluster endpoint access to public and private
6. set region in ~/.aws/config to eu-central-1 and "aws eks update-kubeconfig --name eks-cluster-test" to be able to connect to the cluster
7. Created new role for EC2 workernodes with Policies: AmazonEKSWorkerNodePolicy, AmazonEC2ContainerRegistryReadOnly, AmazonEKS_CNI_Policy named "eks-node-group-role"
8. Added node group to EKS cluseter named "eks-node-group" with created IAM role before
9. Configured Image "Amazon Linux", type "t3.small", disk size "20GiB"
10. scaling min max to 2 (default)
11. added private ssh key. Firewall access set to all
12. Checked if EC2 got launched

--------------------------------------------------

Configure autoscaling in EKS cluster
1. Created custom policy, please see autoscaling-policy.json (copied from gitlab) named "node-group-autoscale-policy"
2. added this custom policy to role eks-node-group-role
3. kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml on terminal
4. cluster-autoscaler got deployed with command above
5. edited deployment: added safe-to-evict=false. edited clustername. added - --blanace-similiar-node-groups - --skip-nodes-with-system-pods=false. set image version to 1.18.3
6. checked all pods in namespace kube-system
7. set min nodes to 1, afterwards autoscaler ran again and scaled to one node
8. copied nginx.yaml from gitlab
9. applied nginx.yaml
10. checked for loadbalancer in AWS console
11. set replicas to 20 to let autoscaler do its thing. set back to 1 afterwards

--------------------------------------------------

create fargate profile for EKS cluster
1. created new role: Use case EKS - Fargate pod, policie: AmazonEKSFargatePodExecutionRolePolicy, name: eks-fargate-role
2. Created fargate profile: name: dev-profile, selected role: eks-fargate-role, namespace: dev (also added to nginx.yaml), match labels: key = profile value = fargate (also added in nginx.yaml)
3. created namespace "dev" and applied nginx.yaml 
4. deleted node group and fargate profile. deleted cluster
5. deleted roles

--------------------------------------------------

Create EKS cluster with eksctl command line tool
1. installed eksctl in WSL
2. created cluster in WSL, please see eksctl-create-cluster-cmd.txt

--------------------------------------------------

Deploy to EKS Cluster from Jenkins Pipeline
1. installed kubectl, aws-iam-authenticator inside jenkins container
2. copied config.yaml from gitlab, added cluster name, API server endpoint and certificate data
3. copied config to docker container with docker cp
4. created credentials in jenkins using secret text for AWS
5. created jenkinsfile with AWS key ids and deployment sh command
6. created new pipeline in jenkins and tested deployment
7. checked for new created pod (kubectl get pods)
8. removed API server endpoint and certificate data of cluster from config.yaml to be safe
9. renamed jenkinsfile to JenkinsfileAWS

--------------------------------------------------

BONUS: Deploy to LKE Cluster from Jenkins Pipeline
1. created new cluster on linode
2. exported kubeconfig.yaml
3. added credential with secret file, using the kubeconfig.yaml
4. installed kubernetes CLI plugin.
5. created new Jenkinsfile with "withKubeConfig"
6. deployed to linode with jenkinsfile
7. removed server endpoint from jenkinsfile to be safe
8. renamed jenkinsfile to JekinsfileLKE

--------------------------------------------------

Complete CI/CD Pipeline with EKS and DockerHub
1. cloned java-maven-app from gitlab
2. in container: apt update && apt install gettext-base -y
3. created docker-registry secret

--------------------------------------------------

Complete CI/CD Pipeline with EKS and ECR
1. created private repo on AWS
2. created "ecr-credentials" in jenkins
3. created docker secret for AWS
4. changed DOCKER_REPO_SERVER in Jenkinsfile

--------------------------------------------------

Reference: DevOps Bootcamp and TWN