# Practice explaining the lab

## One-minute walkthrough

I created an EKS cluster in the AWS Console. EKS runs the Kubernetes control plane, and I added a managed node group with one EC2 worker to run applications. I checked subnet routing and public IP assignment so the worker could reach required services.

I connected PowerShell using the AWS CLI. After fixing my IAM user's permissions and cluster access entry, I used kubectl to create an Nginx Deployment. I exposed it using a LoadBalancer Service and verified the welcome page in a browser.

I scaled the Deployment to three pods, then deleted one. Kubernetes replaced it to restore the requested three replicas. I retained the environment for class. Application YAML is now prepared in this repository; Terraform automation is a future improvement.

## Remember each role

| Component | Plain explanation | Basketball memory aid |
| --- | --- | --- |
| VPC | Network boundary containing the subnets | Arena |
| Subnet | Network segment in one Availability Zone | A section of the arena |
| Route table | Decides where network traffic goes | Directions between sections |
| Internet gateway | Internet route for the public subnet | Arena entrance |
| IAM role/policy | Permissions for AWS identities/services | Credential and access rules |
| EKS control plane | Directs and reconciles Kubernetes work | Coaching staff |
| Node group | Manages the EC2 worker capacity | Roster of available courts |
| Node | Machine that runs pods | Court where players perform |
| Image | Packaged software used to start a container | Prepared playbook |
| Container | Running instance of packaged software | Player executing the play |
| Pod | Kubernetes unit containing the container | Assigned playing spot |
| Deployment | Declares desired app configuration and replica count | Coach requesting three active spots |
| ReplicaSet | Maintains the required pod count for the Deployment | Assistant tracking filled spots |
| Service | Stable access point selecting application pods | Stable team contact point |
| Load balancer | Routes incoming connections toward the application | Entrance directing visitors |
| AWS CLI | Calls AWS APIs | Tool for talking to arena management |
| kubectl | Calls the Kubernetes API | Tool for talking to coaching staff |
| PowerShell | Terminal where commands run | Your communication desk |
| kubeconfig | Cluster connection and authentication configuration | Saved contact instructions |
| CoreDNS | Name discovery within Kubernetes | Internal directory |
| VPC CNI | Pod network connectivity/IP management | Connections across the arena |
| kube-proxy | Implements Service networking on nodes | Routing toward active spots |

The analogies are memory aids; use the plain explanations when presenting.

## Screen-share sequence

1. EKS console: show mycluster Active.
2. Worker group: show mycluster-workers Active and one desired worker.
3. PowerShell: kubectl config current-context.
4. kubectl get deployment class: explain desired versus ready replicas.
5. kubectl get pods -o wide: explain three pods can share one node.
6. kubectl get service class: show the external hostname.
7. Browser: open the hostname using http://.

## Recall practice

Explain each answer aloud before reading it:

- Why did deleting a pod create another? The Deployment's ReplicaSet reconciled the actual count back to three.
- Does scaling to three pods create three EC2 machines? No. Pod replicas and node counts are separate.
- Did we use Terraform? No. We used the console for AWS infrastructure and kubectl for applications.
- Why did IAM need two fixes? DescribeCluster permits fetching AWS cluster details; EKS access policies authorize Kubernetes operations.
- Does closing PowerShell stop the application? No. It runs in AWS.
- Can YAML rebuild the entire AWS cluster? No. These manifests recreate the application inside an existing working cluster.
- Why keep the Service? It keeps a stable application access point while pods are replaced.
- Why is one worker a limitation? Its failure affects all three application pods.

Practice the one-minute walkthrough twice, then explain the project without reading.
