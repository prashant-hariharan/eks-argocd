# Deployment using Argo cd



## Step-00:  Pre-requisites
- Kubernetes Cluster
  - You can create a kuberentes cluster on aws using eks by following the blog: https://prashant-hariharan.blogspot.com/2024/04/kubernetes-made-easy-with-aws-eks.html
  
  - Alternatively, you can setup your own kuberentes cluster using minikube on your local machine
- docker image. You can re-use my publicly hosted docker image **prashantdocker87/eks-demo:1.0** 



## Step-01: Install argo cd on your Cluster

```
# Install Argo CD 
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Get List of components installed
kubectl get all -n argocd
kubectl -n argocd get pods    
```
Wait for all the pods to be in ready state



## Step-02: deploy argo cd configuration
- We need to define a configuration file to instruct argo cd to deploy our respective application.
  Please refer to the file application.yaml
  
  Install the configuration to the kuberentes cluster

```                   
kubectl -n argocd apply -f application.yaml

```
This will try to deploy the aretfacts mentioned in the dev folder onto our kubernetes cluster.




## Step-03: Argo CD UI
Argo CD UI can give you a visual representation of your cluster.

- Enabling port forwarding to access the UI
```
kubectl port-forward svc/argocd-server 8080:443 -n argocd
```

We are basically try to access the argocd-server from our localhost:8080

- The ui will prompt for user name and password
  
  **username: admin** 
  

To get the password run the following command:
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## Step-04: Application verification
Argo cd would have synced and deployed the docker image to kuberentes.
You can view the deployment using:

```                   
kubectl -n myapp get pods

```
 
## Step-05: updating version
Imagine in a project scenario, you want to deploy a new version of your applciation to your kuberentes cluster.
You can do that by simply changing the version of the image in the deployment.yaml
For example: change image from prashantdocker87/eks-demo:1.0 to prashantdocker87/eks-demo:2.0
(Assuming 2.0 version is available in your registry)

Argocd will automatically detect the changes and deplyo the applciation.
