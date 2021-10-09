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

#### How to install Minikube in Mac:
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


