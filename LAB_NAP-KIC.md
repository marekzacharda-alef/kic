LAb guide updated 

https://clouddocs.f5.com/training/community/nginx-app-protect-standalone/html

1. check if arcadia finance k8s cluster is running 

A) Protect Arcadia with NGINX App Protect in **host VM** 
  1. clear old installation 
  ```/home/centos/lab-files/remove-app-protect-cleanup.sh``` 
  
  2.  Run the CI/CD pipeline from Gitlab
      Steps:
      RDP to the Jumphost with credentials user:user
      Open Firefox and open Gitlab (if not already opened)
       Select the repository nap-deploy-centos and go to CI /CD
       
B) Protect Arcadia with NGINX App Protect in **Docker**
  1. we will use a signature package update as a trigger
  2. ssh to CICD server > check the latest attack signatures 
  ubuntu@cicd
  ```sudo yum --showduplicates list app-protect-attack-signatures```
  
  app-protect-attack-signatures.x86_64            ** 2023.05.09**-1.el7.ngx
  
  3. In GitLab, click on Repository and Tags in the left menu
  4. Create a new tag and give it a name (though the tag name is arbitrary and the job will run with any tag name) Example: Sig-2021.07.13 where ideally <version_date> should be replaced by the package version information found in the result of the yum info step above.         But it does not matter, you can put anything you want in this tag.

  5. Click Create tag
     At this moment, the Gitlab CI pipeline starts
     
     
  Make policy dynamic 
     >docker run --interactive --tty --rm --name app-protect -p 80:80 \
    --volume /home/ubuntu/lab-files/nginx.conf:/etc/nginx/nginx.conf \
    --volume /home/ubuntu/lab-files/customized-policy:/etc/nginx/conf.d \
    docker:443/app-protect:2023.05.09
     
     
     
 C) Protect Arcadia with **KIC NAP** 
   1.  ssh to k8s VM 
   ``` helm list```
   ```helm uninstall nginx-ingress```
   
   ```helm repo add nginx-stable https://helm.nginx.com/stable
  helm repo update

  helm install plus nginx-stable/nginx-ingress \
  --namespace ingress \
  --set controller.kind=deployment \
  --set controller.replicaCount=1 \
  --set controller.nginxplus=true \
  --set controller.image.repository=private-registry.nginx.com/nginx-ic-nap/nginx-plus-ingress \
  --set controller.image.tag=2.4.2 \
  --set controller.appprotect.enable=true \
  --set controller.serviceAccount.imagePullSecretName=regcred \
  --set controller.service.type=NodePort \
  --set controller.service.httpPort.nodePort=30080 \
  --version 0.15.2
  ```
  Now, it is time to configure the Ingress Controller with CRD ressources (WAF policy, Log profile, Ingress routing …)

Execute the following commands to deploy the different resources

cd /home/ubuntu/lab-files/ingress

kubectl apply -f ap-dataguard-policy.yaml
kubectl apply -f ap-logconf.yaml
kubectl apply -f nap-waf.yaml
kubectl apply -f virtual-server-waf.yaml


