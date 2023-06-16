# Deploying to EKS Using GitOps and ArgoCD - Real World Project

In this project, we will deploy a simple Python Flask API application using GitOps, ArgoCD, and Kubernetes to deploy it on EKS

![image](https://github.com/aadilraza339/python-flask-api/assets/47937273/9f9a5ed1-4d3e-47ae-9d81-8e22cdd192ba)

### Prerequiresties
- [Docker Hub](https://hub.docker.com/) account To push image on it 
- EKS cluster
- Argocd 
- Deployment manifest file

Let's start 

1. We have to create eks cluster.
```
 eksctl create cluster
```
![image](https://github.com/aadilraza339/python-flask-api/assets/47937273/b507e432-36e7-460c-8ff3-3522e50755fb)

2. Install Argo CD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
This will create a new namespace, argocd, where Argo CD services and application resources will live.
![image](https://github.com/aadilraza339/python-flask-api/assets/47937273/fd15a817-63d4-4962-aa71-6f2a3dcffa60)


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

![Screenshot from 2023-06-16 19-45-40](https://github.com/aadilraza339/python-flask-api/assets/47937273/8252f762-a467-4996-9e38-5f1e72d8bfb4)

![image](https://github.com/aadilraza339/python-flask-api/assets/47937273/62842584-4f88-4096-9dd8-65dbcb7e1dd1)


6. To log in to ArgoCD, we need a username and password. Please run the following command to retrieve the credentials
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
![WhatsApp Image 2023-06-16 at 7 57 39 PM](https://github.com/aadilraza339/python-flask-api/assets/47937273/72c95df3-b64a-494f-9e73-cf6d21aea5a1)

Username will be `admin`


7. To clone the python-flask-api repository and add secret variables in GitHub Actions, follow these steps:
Clone the `python-flask-api` repository to your local machine using the Git command:
```
git clone <repository_url>
```
Replace <repository_url> with the actual URL of the python-flask-api repository.
- Go to the GitHub website and navigate to the python-flask-api repository.
- Click on the "Settings" tab located on the right-hand side of the repository page.
- In the left sidebar, click on "Secrets" under the "Secrets and variables" section.
- On the Secrets page, click on the "New repository secret" button.
- In the "Name" field, enter DOCKERHUB_USERNAME.
- In the "Value" field, enter your Docker Hub username.
- Click on the "Add secret" button to save the DOCKERHUB_USERNAME secret.
- Repeat same steps for the DOCKERHUB_TOKEN secret. Use DOCKERHUB_TOKEN as the `name` and your Docker Hub token as the `value`.
Once you have added both secrets (DOCKERHUB_USERNAME and DOCKERHUB_TOKEN) to the repository, you can use them in your GitHub Actions workflows as needed.

8. Clone `python-flask-deployment` [repository](https://github.com/aadilraza339/python-flask-deployment) and Create Application in argocd.
```
kubectl apply -f argocd-application.yaml
```
After running this command, our Python application will go live.

9. To open our Python application in a browser, we will need the external IP. Run the following command to obtain the URL:
```
kubectl get svc
```
![WhatsApp Image 2023-06-16 at 8 04 59 PM](https://github.com/aadilraza339/python-flask-api/assets/47937273/192ea645-6e50-4390-9491-d6b4e15a477d)

Boom! 🎉 Now we can access our Python application.
Copy this URL and paste it in a browser


10. Let's assume that one of the developers is working on the python-flask-api project. If they want to deploy their changes live, they simply need to follow these steps:

Make the necessary changes in the app.py file within the main branch.
Run the following commands:
- git add app.py
- git commit -m "Update hello_world message"
- git push
Now, we need to wait for the application image to build and be pushed live.

After waiting, you can refresh our live application to check if the changes have been successfully deployed or not.

For a better understanding, you can watch a video tutorial.

### Happy coding :)
