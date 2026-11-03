# Troubleshooting from the actual lab

## EC2 launch denied

The initial t3.medium worker failed with an instance-not-Free-Tier-eligible error.

Queried eligible types:

```powershell
aws ec2 describe-instance-types --region us-east-1 --filters Name=free-tier-eligible,Values=true --query "InstanceTypes[].InstanceType" --output table
```

The returned list included t3.small. Deleted the failed node group and recreated it with t3.small, retaining the cluster, worker role and subnet.

## DescribeCluster AccessDenied

The CLI user jwatsUser lacked eks:DescribeCluster. Added an IAM inline policy allowing that action on the specific cluster ARN, then reran update-kubeconfig.

Account IDs and ARNs are intentionally not embedded in this public project template.

## Kubernetes asked for credentials

The CLI user did not have an EKS access entry. Created a Standard entry for its IAM user ARN and associated AmazonEKSEditPolicy at Cluster scope. kubectl get pods then succeeded.

## No resources found in default namespace

This indicated successful access to an empty namespace, not a broken cluster. Created the Nginx Deployment next.

## Network checks

Confirmed the worker public subnet had auto-assign public IPv4 enabled and an active 0.0.0.0/0 route to an internet gateway.
