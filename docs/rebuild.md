# Rebuild options

## Application: ready as YAML

The repository contains a Deployment and Service matching the basic classroom configuration. Apply them only after connecting to the intended working cluster. These files have not been applied during packaging.

## AWS infrastructure: manual checklist

1. Check AWS identity, region us-east-1, CLI and compatible kubectl.
2. Check or create VPC, DNS support, subnets, routes, internet access and security dependencies. Do not assume a default VPC exists.
3. Create the EKS control-plane role with AmazonEKSClusterPolicy.
4. Create mycluster through custom configuration with Auto Mode disabled, standard version support and appropriate cluster administrator access.
5. Select private control-plane subnets in multiple zones and public/private endpoint access.
6. Install VPC CNI, CoreDNS and kube-proxy.
7. Create the EC2 worker role. The console lab attached AmazonEKSWorkerNodePolicy, AmazonEKS_CNI_Policy, AmazonEC2ContainerRegistryReadOnly and AmazonElasticContainerRegistryPublicReadOnly.
8. Create mycluster-workers with one eligible x86_64 worker, 20 GiB disk and desired/min/max 1/1/2. Recheck current account eligibility rather than assuming an instance type is allowed.
9. For this lab's public worker approach, select the verified public subnet with public IPv4 assignment and internet gateway routing.
10. Wait for Active; configure DescribeCluster permission and the CLI identity's EKS access entry.
11. Run update-kubeconfig, check the context, then apply k8s/.

IAM permissions, available versions, policies, console fields and account restrictions can change. This records the actual classroom configuration rather than a universal current AWS recipe.

## Terraform: planned

Terraform could provision and track the VPC/network dependencies, IAM roles, EKS cluster, access entries and node group. This repository currently has no .tf files and no Terraform state.

Do not point new Terraform configuration at the existing resources and assume it manages them. Either import them carefully or build a separately named environment after reviewing costs. Preserve state securely; do not commit state or credentials.

## AWS CLI / eksctl: planned

Scripted AWS API calls or a tested eksctl configuration are other infrastructure rebuild options. Neither has been implemented here. Application YAML alone is not a complete infrastructure rebuild.
