LAb guide updated 

https://clouddocs.f5.com/training/community/nginx-app-protect-standalone/html

1. check if arcadia finance k8s cluster is running 

A) Protect Arcadia with NGINX App Protect in **host VM** 
  1. /home/centos/lab-files/remove-app-protect-cleanup.sh 
  clear old installation 
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
  
  
