 # Jenkins App using  Kubernetes with Docker Container

We are going to perform following tasks in Mac OS.

## Tasks

1. Goal is to create a docker based Jenkins application (app-A). Push the code in this app to a GitHub repo and share the URL
2. This application (app-A) has to be Jenkins based. It watches an external public GitHub repository which is a node or java app (app-B) which can be built using a ‘npm install’ or ‘mvn clean install’. The watching of this external app (app-B) is by using web-hooks
3. Whenever a PR happens to this external repo, it triggers build and test steps. On completion of build and test steps, the Jenkins app (app-A) has to post a comment on the PR indicating success. If the build/test fails then the comment has to denote failure


### Prequisite:
1. Helm 
2. K8s
4. Ngrok
5. Minikube
6. Docker
7. GIT
8. GITHUB account

#### How to install Helm Packager in Mac:
Helm is a package manager for Kubernetes. 
we are going to install the tools and softwares using homebrew. 

If you dont have homebrew , install by following command
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Type the following command in  mac terminal 
```sh
brew install helm
```

Documentation link: [HELM Installation](https://helm.sh/docs/intro/install/)

#### How to install Docker in Mac:
Docker is an open source containerization platform. It enables developers to package applications into containers—standardized executable components combining application source code with the operating system (OS) libraries and dependencies required to run that code in any environment. Containers simplify delivery of distributed applications, and have become increasingly popular as organizations shift to cloud-native development and hybrid multicloud environments.

Docker installation on Mac:  [Docker Install](https://docs.docker.com/desktop/mac/install/)

Check docker version
```
rajaks@RAJAKS-M-42S5 ~ % docker version
Client:
 Cloud integration: 1.0.17
 Version:           20.10.8
 API version:       1.41
 Go version:        go1.16.6
 Git commit:        3967b7d
 Built:             Fri Jul 30 19:55:20 2021
 OS/Arch:           darwin/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.8
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.6
  Git commit:       75249d8
  Built:            Fri Jul 30 19:52:10 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.9
  GitCommit:        e25210fe30a0a703442421b0f60afac609f950a3
 runc:
  Version:          1.0.1
  GitCommit:        v1.0.1-0-g4144b63
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
rajaks@RAJAKS-M-42S5 ~ %
```

#### How to install Minikube in Mac:
Minikube is a utility you can use to run Kubernetes (k8s) on your local machine. It creates a single node cluster contained in a Mac/Linux/Windows.
Install Minikube with following command

```
brew update && brew install kubectl && brew cask install minikube
```

Start Minikube
```
minikube start --driver=none
```
Check Minikube status
```
rajaks@RAJAKS-M-42S5 ~ % minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

```

#### How to install ngrok
Ngrok exposes local servers behind NATs and firewalls to the public internet over secure tunnels. [Ngrok Download Documentation](https://ngrok.com/download)
Using ngrok, we can expose our localport to Public Internet. so that we can configure webhook from GITHUB to Jenkins. 

Ngrok Documentation Process: [Ngrok Steps)[https://ngrok.com/docs]

step:
 1. download the ngrok in mac. unzip it 
 2. create a account in ngrok site (https://dashboard.ngrok.com/login)
 3. Note down the <Auth Token> 
 4. type the following command in the terminal
```
 ./ngrok authtoken 1Z8EVGxy5Y5nwwVgSgmeTI8Z4xe_6DFgfJDvSM8kD7wSB5XZi
```
 5. Ngrok is installed.
 

You are ready to go now.  Now , we are going to deploy the jenkins using helm chart in Minikube. 
 
 ### Jenkins Chart Description
 
   Clone the Repository in the workspace. 
  
 #### template/deployment.yaml
   This is deployment template which replicas,containers,volumes,labels,namespace. 
 
 #### template/service.yaml
   This is service template which expose the jenkins port.
 
 #### template/service-jnlp.yaml
   This is the service template for Jnlp process which exposes jnlp port. 
 
 #### values.yaml
   This is the input file for template where we push the configurations. Image,port numbers,namespace etc..
 
 #### README.md
   This is the Read me file for the Helm charts. 
 
 
 ### How to deploy the Jenkins App
 
 1. clone the Git repo
 2. Install the helm chart
```
 rajaks@RAJAKS-M-42S5 jenkins-helm-chart % helm install jenkins . -f values.yaml
NAME: jenkins
LAST DEPLOYED: Sat Oct  9 19:07:12 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services jenkins)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
rajaks@RAJAKS-M-42S5 jenkins-helm-chart %
 ```
 3. To check the Helm chart deploy
 ```
 rajaks@RAJAKS-M-42S5 jenkins-helm-chart % helm list
NAME   	NAMESPACE	REVISION	UPDATED                             	STATUS  	CHART        	APP VERSION
jenkins	default  	1       	2021-10-09 19:07:12.487584 +0530 IST	deployed	jenkins-0.1.0	1.16.0
rajaks@RAJAKS-M-42S5 jenkins-helm-chart %
 ```
 4. To check the service exposed
 ```
 rajaks@RAJAKS-M-42S5 jenkins-helm-chart % kubectl get svc
NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
jenkins        NodePort    10.110.13.239   <none>        8080:30000/TCP   56s
jenkins-jnlp   ClusterIP   10.101.126.2    <none>        50000/TCP        56s
kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP          5d22h
rajaks@RAJAKS-M-42S5 jenkins-helm-chart %
 ```
 5. We need to create a service tunnel.
 ```
 rajaks@RAJAKS-M-42S5 jenkins-helm-chart % minikube service jenkins
|-----------|---------|-------------|---------------------------|
| NAMESPACE |  NAME   | TARGET PORT |            URL            |
|-----------|---------|-------------|---------------------------|
| default   | jenkins | http/8080   | http://192.168.49.2:30000 |
|-----------|---------|-------------|---------------------------|
🏃  Starting tunnel for service jenkins.
|-----------|---------|-------------|------------------------|
| NAMESPACE |  NAME   | TARGET PORT |          URL           |
|-----------|---------|-------------|------------------------|
| default   | jenkins |             | http://127.0.0.1:56048 |
|-----------|---------|-------------|------------------------|
🎉  Opening service default/jenkins in default browser...
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
 ```
 after this,  the jenkins will fired in the browser
 
 6. Get the password from the jenkins pods
  kubectl logs <jenkins pod name>
 ```
 Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

6af491493bf246c2a210ae1f98c95bd5

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

 ```
 7. Use this password (this may change in every deployment) in the jenkins link and proceed further. 
 8. Create a admin user/password at first time. 
 
 
 ### Make Tunnel using Ngrok for GITHUB Web hook.
 Using ngrok, we can expose our localport to Public Internet. so that we can configure webhook from GITHUB to Jenkins. 
 ngrok http <jenkins tunnel port after the 'minikube service jenkins` command. In our case, tunnel port is 56048.
  
  ```
 ngrok by @inconshreveable                                                (Ctrl+C to quit)

Session Status                online
Account                       raja.mom@gmail.com (Plan: Free)
Version                       2.3.40
Region                        United States (us)
Web Interface                 http://127.0.0.1:4043
Forwarding                    http://8cd5-106-51-232-203.ngrok.io -> http://localhost:560
Forwarding                    https://8cd5-106-51-232-203.ngrok.io -> http://localhost:56

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
  ```
  Our jenkins is now publicly accessible --> 'https://8cd5-106-51-232-203.ngrok.io'
  
  
