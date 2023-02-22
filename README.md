# jenkins-helm-kubernetes
## Demo to setup Jenkins on Kubernetes Cluster using helm

### In this demo, we will see how to access Jenkins Dashboard on Kubernetes Cluster using Helm chart.

OS: Ubuntu 21.10

Docker Version: 20.10.17

Minikube Version: v1.29.0
commit: ddac20b4b34a9c8c857fc602203b6ba2679794d3 

Kubectl Version: 1.26

To see how to install Minikube and Kubectl, [click here](https://github.com/riddhigala09/Kubectl-Minikube-Ubuntu.git)

Helm Version: v3.11.1

To see how to install Helm, [click here](https://github.com/riddhigala09/installing-helm.git)

**We will be setting up Jenkins on Minikube Cluster.** 

Step 1: Start Minikube cluster:

    > minikube start

  ![Screenshot from 2023-02-21 17-38-55](https://user-images.githubusercontent.com/122020679/220341355-101ddb8e-d6c4-4a6f-95ef-e185a801b127.png)

  Cluster started
  
  ![Screenshot from 2023-02-21 17-43-24](https://user-images.githubusercontent.com/122020679/220342402-a6471508-05be-48b9-aace-67048997cf8a.png)

    > minikube status
    
  ![Screenshot from 2023-02-21 17-44-10](https://user-images.githubusercontent.com/122020679/220342576-5a769d38-45f7-4ef8-ab1e-706494e9d8db.png)

Step 2: Create directory helm-jenkins on desktop

    > mkdir helm-jenkins
    
  ![Screenshot from 2023-02-21 17-49-13](https://user-images.githubusercontent.com/122020679/220343428-275f4fa3-f393-43a6-8aef-7d1df0b43b7b.png)

Step 3: Create Namespace jenkins

    > kubectl create namespace jenkins
    
    > kubectl get ns
  
  ![Screenshot from 2023-02-21 17-54-38](https://user-images.githubusercontent.com/122020679/220344443-aec2cefc-7a6c-4b15-9579-fe062e456588.png)
  
  We will be working in this namespace only.
  
Step 4: Adding helm repo

    > helm repo add jenkins https://charts.bitnami.com/bitnami

  ![Screenshot from 2023-02-21 18-01-05](https://user-images.githubusercontent.com/122020679/220345785-6585c0c4-7290-4b56-bc20-2d9c75e691d3.png)

Step 5: Installing chart

    > helm install jenkins jenkins/jenkins -n jenkins
    
  ![Screenshot from 2023-02-21 18-10-49](https://user-images.githubusercontent.com/122020679/220347644-fa63142c-811a-4aba-a61f-4ccc59d420e4.png)

  Copy and Paste the output in a notepad
  
  ![Screenshot from 2023-02-22 11-19-34](https://user-images.githubusercontent.com/122020679/220534549-5aa2dcd2-f0b3-4dd1-9786-bc002fdf9193.png)

  We will need this information to access Jenkins Dashboard.
  
Step 6: Enter Command

    > kubectl get all -n jenkins
    
  ![Screenshot from 2023-02-21 18-15-08](https://user-images.githubusercontent.com/122020679/220348505-232186a0-9fa4-45d3-b7fd-e813ca4cade1.png)crash

  In the pod status you migtt get "pending" or "CrashLoopBackOff", Don't worry about it, it's because we haven't change our YAML file.
  
Step 7: Fetching values.yaml

   Enter Command:

    > helm get values jenkins -n jenkins -a > values.yaml
    
    > ls
    
   This will fetch values.yaml file from chart
   
    > vim values.yaml
   
   ![Screenshot from 2023-02-21 18-29-58](https://user-images.githubusercontent.com/122020679/220351334-1067e841-e564-4188-b402-79197c2cac91.png)
   
Step 8: Create Storage Class

  Command: 
    
    > vim sc.yaml
    
    > kubectl apply -f sc.yaml -n jenkins
    
    > kubectl get sc -n jenkins
    
   ![Screenshot from 2023-02-21 18-39-46](https://user-images.githubusercontent.com/122020679/220353744-95ba9ee5-c690-49c4-8f64-df2195705953.png)

Step 9: Create Persistent Volume

    > vim pv.yaml
    
    > kubectl apply -f pv.yaml -n jenkins
    
    > kubectl get pv -n jenkins
    
   ![Screenshot from 2023-02-21 18-52-20](https://user-images.githubusercontent.com/122020679/220356387-c852c40f-7034-4966-a002-c221e9c5274b.png)

Step 10: Create Persistent Volume Claim

  Before creating new persistent volume claim, we need to delete old persistent volume claim
  
    > kubectl get pvc -n jenkins
    
    > kubectl delete pvc <pvc_name> -n jenkins
    
  ![Screenshot from 2023-02-21 18-57-00](https://user-images.githubusercontent.com/122020679/220357581-c842092a-9ee2-4d19-8026-b869a4e5fd2b.png)

    > vim pvc.yaml
    
    > kubectl apply -f pvc.yaml -n jenkins
    
    > kubectl get pvc -n jenkins
    
  ![Screenshot from 2023-02-21 19-00-33](https://user-images.githubusercontent.com/122020679/220358108-aa8d3e09-7140-4856-a3b7-546a22476849.png)
  
Step 11: Editing deployment.apps

    > kubectl get pvc -n jenkins
    
  Copy the pvc name: jenkins-pv-claim
   
  ![Screenshot from 2023-02-22 10-51-30](https://user-images.githubusercontent.com/122020679/220530134-bbe07a54-2efe-4a54-aec1-b2c37fb9f298.png)
  
  Now open deployment.apps edit it using following command:

    > kubectl edit deployment.apps jenkins -n jenkins
    
  In volume, modify "jenkins" to "jenkins-pv-claim"
    
  Before:
  
  ![Screenshot from 2023-02-22 10-57-09](https://user-images.githubusercontent.com/122020679/220531179-932cc89c-49d9-4d96-8513-bc0573727b69.png)
  
  After:
  
  ![Screenshot from 2023-02-22 10-59-11](https://user-images.githubusercontent.com/122020679/220531394-81de7f65-cb75-436d-ad08-239921786ef6.png)
  
  ![Screenshot from 2023-02-22 10-59-58](https://user-images.githubusercontent.com/122020679/220531466-8a610ef3-72c0-426e-8eb0-c031ce15d99d.png)

Step 12: Get Pods

    > kubectl get pods -n jenkins
    
  ![Screenshot from 2023-02-22 11-01-23](https://user-images.githubusercontent.com/122020679/220531896-8f7aa0df-e10b-4482-b956-bab919fcde84.png)
  
  Our pod is running now.
  
Step 13: Accessing Jenkins Dashboard

  Since we are using minikube, we will need to minikube ip and the port number from storage volume class
  
    > minikube ip
    
    > kubectl get svc -n jenkins
    
  ![Screenshot from 2023-02-22 11-14-55](https://user-images.githubusercontent.com/122020679/220533767-6f188ec3-3c41-40c5-84eb-c2fc344cc481.png)

  Copy minikube ip and port number in browser
  
    > <minikube ip>:<port number>
    
   ![Screenshot from 2023-02-22 11-17-30](https://user-images.githubusercontent.com/122020679/220534187-68b651a4-b8d4-40c0-b920-088e4705c958.png)
  
Step 14: Get Jenkins dashboard password.

  Copy and paste the command which we got from the output of "helm install"
  
  ![Screenshot from 2023-02-22 11-21-09](https://user-images.githubusercontent.com/122020679/220534794-887502e8-d124-487b-82ed-3dcd36781a47.png)

  Command: 
  
    > echo Password: $(kubectl get secret --namespace jenkins jenkins -o jsonpath="{.data.jenkins-password}" | base64 -d)
    
  ![Screenshot from 2023-02-22 11-22-54](https://user-images.githubusercontent.com/122020679/220534946-80d56088-e862-40be-9f6f-fb525d27efe3.png)

Step 15: Login

   Copy the Password and go to Jenkins Dashboard
   
   Enter username and password
   
   ![Screenshot from 2023-02-22 11-25-08](https://user-images.githubusercontent.com/122020679/220535374-0e283aca-888c-4f99-b057-d9bece1e823a.png)
   
   Click "sign in"
   
   ![Screenshot from 2023-02-21 16-55-13](https://user-images.githubusercontent.com/122020679/220535942-10e446a8-ade1-49ed-8159-2ef68300d5d2.png)

***We have successfully completed this tutorial!***
