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
  
  ![Screenshot from 2023-02-21 18-12-00](https://user-images.githubusercontent.com/122020679/220347885-934d951d-9ef1-41e2-94e0-a86780015487.png)

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

  Before craeting new persistent volume claim, we need to delete old persistent volume claim
  
    > kubectl get pvc -n jenkins
    
    > kubectl delete pvc <pvc_name> -n jenkins
    
  ![Screenshot from 2023-02-21 18-57-00](https://user-images.githubusercontent.com/122020679/220357581-c842092a-9ee2-4d19-8026-b869a4e5fd2b.png)

    > vim pvc.yaml
    
    > kubectl apply -f pvc.yaml -n jenkins
    
    > kubectl get pvc -n jenkins
    
  ![Screenshot from 2023-02-21 19-00-33](https://user-images.githubusercontent.com/122020679/220358108-aa8d3e09-7140-4856-a3b7-546a22476849.png)
