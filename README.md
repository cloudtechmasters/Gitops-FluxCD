# Gitops-FluxCD

# Pre-requisites:
    - Install GIT
    - EKS Cluster
    - Install fluxctl
    - Install FluxCD or Flux Operator
# EKS Cluster Setup:
  [EKS Cluster Setup](https://github.com/cloudtechmasters/eks-cluster-setup/blob/master/README.md)
# Install fluxctl:
    wget https://github.com/fluxcd/flux/releases/download/1.21.0/fluxctl_linux_amd64
    mv fluxctl_linux_amd64 fluxctl
    chmod +x fluxctl
    mv fluxctl /usr/bin/
    fluxctl version
# Install FluxCD or Flux Operator:
  Create the flux namespace:
    
    kubectl create ns springboot
Then, install Flux in your cluster (replace YOURUSER with your GitHub username):
  Syntax:    
    
    export GHUSER="YOURUSER"
        fluxctl install \
        --git-user=${GHUSER} \
        --git-email=${GHUSER}@users.noreply.github.com \
        --git-url=git@github.com:${GHUSER}/flux-get-started \		--> Replace URL with our Git Repo URL
        --git-path=namespaces,workloads \				            --> Replace with folder inside Git Repo
        --namespace=flux | kubectl apply -f -
  
  Changed as:
    
    export GHUSER="naresh240"
    fluxctl install \
    --git-user=${GHUSER} \
    --git-email=${GHUSER}@users.noreply.github.com \
    --git-url=git@github.com:${GHUSER}/Gitops-FluxCD \
    --git-branch=master \
    --git-path=deploy-k8s \
    --namespace=springboot | kubectl apply -f -
  Edit Deployment file
  
    kubectl edit deploy flux -n springboot
    
    Add "--sync-garbage-collection"
    -----------------------------------
    - args:
      - --memcached-service=
      - --sync-garbage-collection=true    
    -----------------------------------
  ![image](https://user-images.githubusercontent.com/58024415/108061521-75d4c280-707e-11eb-897e-63c186f6d5a0.png)

  Wait for Flux to start:
  
    kubectl -n springboot rollout status deployment/flux
      
  Check if Flux deployment is successful or not:
  
    kubectl get deploy -n springboot
    
  ![image](https://user-images.githubusercontent.com/58024415/108053154-0d341880-7073-11eb-8844-8937f07ba4f2.png)
    
  Authorise Flux CD to Connect to Your Git Repository:
  
  We now need to allow the Flux CD operator to interact with the Git repository, and therefore, we need to add its public SSH key to the repo.
  
  Get the public SSH key using fluxctl:
    
    fluxctl identity --k8s-fwd-ns springboot
    
  In order to sync your cluster state with git you need to copy the public key and create a deploy key with write access on your GitHub repository.
  Open GitHub Repo --> settings --> Deploy Keys
  Add public key inside Deploy Keys, Click on Allow write access check box and then click on Add Key 
  
  ![image](https://user-images.githubusercontent.com/58024415/108053303-3f457a80-7073-11eb-8465-77e750efe8c2.png)

  By default, Flux git pull frequency is set to 5 minutes. You can tell Flux to sync the changes immediately with:
  
    fluxctl sync --k8s-fwd-ns springboot
  
  ![image](https://user-images.githubusercontent.com/58024415/108053429-6ac86500-7073-11eb-88cb-a42e71967f09.png)
  
  Now check wether deployment done in cluster or not:
  
  ![image](https://user-images.githubusercontent.com/58024415/108053527-87fd3380-7073-11eb-927f-b2789613ff4e.png)  
