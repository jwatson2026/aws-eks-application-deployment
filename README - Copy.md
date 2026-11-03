# AWS EKS: Nginx Deployment, Scaling, and Pod Replacement

Author: Joshua Watson

Hands-on training project completed November 2, 2026.

## What I built

I created an Amazon EKS cluster through the AWS Console, added one managed EC2 worker, connected from PowerShell, and deployed Nginx with kubectl. I exposed the application through an AWS load balancer, scaled it to three pods, and verified that Kubernetes replaced a deliberately deleted pod.

This is a learning lab with a single worker, not a production architecture. Infrastructure was created manually; this repository does not contain Terraform or claim automated infrastructure provisioning.

## Architecture

Browser → AWS load balancer → Kubernetes Service → Nginx pods on an EC2 worker.

The EKS control plane manages Kubernetes scheduling and reconciliation. The Deployment manages a ReplicaSet that maintains three application pods.

## Actual lab configuration

| Component | Configuration |
| --- | --- |
| Region | us-east-1 |
| Cluster | mycluster, Kubernetes 1.37 |
| Cluster role | mycluster-cluster-role |
| Authentication | EKS API and ConfigMap |
| Endpoint | Public and private |
| Network | Existing custom VPC named Default -vpc; 10.0.0.0/16 |
| Control plane networking | Three private subnets in us-east-1a/b/c |
| Worker group | mycluster-workers |
| Worker role | mycluster-node-role |
| Worker | One On-Demand t3.small, AL2023 x86_64, 20 GiB disk |
| Scaling limits | Desired 1, minimum 1, maximum 2 |
| Worker networking | Public subnet in us-east-1a, public IPv4 assignment and internet gateway route |
| Add-ons | VPC CNI, CoreDNS, kube-proxy |
| Application | Nginx, Deployment named class, three replicas |
| Exposure | LoadBalancer Service named class, port 80 |

Despite its name, the VPC was not an AWS default VPC: the subnet console showed Default subnet: No. Actual resource IDs are intentionally omitted.

## Files

- k8s/deployment.yaml: reusable application Deployment.
- k8s/service.yaml: reusable LoadBalancer Service.
- docs/explain-it.md: presentation and recall guide.
- docs/rebuild.md: rebuild boundaries and manual infrastructure checklist.
- docs/troubleshooting.md: actual issues resolved during the lab.

These YAML files were prepared after the console/command-based lab. They reproduce its basic desired application configuration; they are not exports of live objects and have not been applied to the existing cluster.

## Prerequisites and application rebuild

Use an existing working EKS cluster, compatible kubectl, AWS CLI credentials with eks:DescribeCluster permission, and an EKS access entry granting application access. The lab used AmazonEKSEditPolicy at cluster scope.

Run in PowerShell from this repository:

```powershell
aws eks update-kubeconfig --region us-east-1 --name mycluster
kubectl config current-context
kubectl apply -f k8s/
kubectl rollout status deployment/class --timeout=180s
kubectl get deployment class
kubectl get pods -l app=class -o wide
kubectl get service class
```

Verify the context targets the intended cluster before applying. Open the Service's external hostname with http://. A new Service may receive a different hostname. A LoadBalancer Service provisions chargeable AWS resources.

The image is nginx with an implicit latest tag, matching the class exercise. Pin a tested image version/digest for a more reproducible future revision.

## Original hands-on commands

```powershell
kubectl create deployment class --image=nginx
kubectl expose deployment class --port=80 --target-port=80 --type=LoadBalancer
kubectl scale deployment class --replicas=3
kubectl get pods
```

Do not repeat create commands against already existing objects; use apply with the included YAML instead.

To test replacement, copy a current pod name from kubectl get pods, then run kubectl delete pod followed by that name. Run kubectl get pods again to inspect its replacement.

## Observed results

- Browser displayed Welcome to nginx!
- Three application pods showed 1/1 Running.
- Deleting one pod resulted in a new pod with a new name and younger age, restoring the count to three.

These results were observed in the lab session. Add your own screenshots under screenshots/ when available; none are included here.

## Costs and cleanup

The environment was deliberately retained for the next class demonstration. EKS, EC2, EBS, load balancing, public IPv4 addresses, and any other chargeable resources can continue to accrue charges. Free Tier eligibility is not a guarantee the entire lab is free.

After class, when ready to remove the lab:

```powershell
kubectl delete service class
kubectl delete deployment class
```

Verify the AWS load balancer is deleted before removing the worker group and EKS cluster through the console. Review remaining volumes, load balancer resources and any NAT gateways separately. Preserve shared networking unless it is no longer needed.

## Limitations and next improvements

- One worker is a single point of failure; three pods on one worker do not provide multi-node availability.
- Public worker placement was selected for this classroom lab.
- Public cluster endpoint was left broadly reachable, with authenticated access still required.
- Terraform infrastructure, automated CI/CD, TLS, autoscaling and production hardening are future work, not completed features.
- kubectl node listing requires permissions beyond the application's edit policy.

For production-style learning, restrict endpoint access, evaluate private workers and their egress, add resource requests/limits and health probes, pin images, and build/test Terraform as a separate improvement.
