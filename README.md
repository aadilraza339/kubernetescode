# Deploying to EKS Using GitOps and ArgoCD - Real World Project

In this project, we will deploy a simple Python Flask API application using GitOps, ArgoCD, and Kubernetes to deploy it on EKS

![image](https://github.com/aadilraza339/python-flask-api/assets/47937273/9f9a5ed1-4d3e-47ae-9d81-8e22cdd192ba)

### Prerequiresties
- EKS cluster
- Argocd 
- Deployment manifest file

Let's start 

1. We have to create eks cluster.
```
 eksctl create cluster
```
2. Install Argo CD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
This will create a new namespace, argocd, where Argo CD services and application resources will live.

3. Access The Argo CD API Server
By default, the Argo CD API server is not exposed with an external IP. To access the API server, expose the Argo CD API server:
After applying the load balancer, we will obtain an external IP that allows us to access the ArgoCD server and its user interface (UI).
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
5. To obtain the external IP, please paste the following command:
```
kubectl get svc argocd-server -n argocd
```
Copy the external IP and open it in a browser.

6. To log in to ArgoCD, we need a username and password. Please run the following command to retrieve the credentials
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
Username will be `admin` 

7. Clone `python-flask-deployment` [repository](https://github.com/aadilraza339/python-flask-deployment) and Create Application in argocd.
```
kubectl apply -f argocd-application.yaml
```
After running this command, our Python application will go live.

8. To open our Python application in a browser, we will need the external IP. Run the following command to obtain the URL:
```
kubectl get svc
```

Boom! Now we can access our Python application.

9. Let's assume that one of the developers is working on the python-flask-api project. If they want to deploy their changes live, they simply need to follow these steps:

Make the necessary changes in the app.py file within the main branch.
Run the following commands:
- git add app.py
- git commit -m "Update hello_world message"
- git push
Now, we need to wait for the application image to build and be pushed live.

After waiting, you can refresh our live application to check if the changes have been successfully deployed or not.
