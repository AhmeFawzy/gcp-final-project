# GCP-Final-Project :cloud:
Hosting web application on private GKE cluster using Terraform and Docker
## Overview
This project is designed to deploy a secure infrastructure on `Google Cloud Platform (GCP)` using `Terraform`. The infrastructure includes:
- Virtual Private Cloud (VPC) with two subnets 
    - Management Subnet & Restricted Subnet. 
- The Management Subnet contains:
    - NAT gateway & Private VM 
- The Restricted Subnet contains:
    - `Private standard GKE cluster (private control plan)`.
    
The VM must be private, and the Restricted Subnet must not have access to the internet. All images deployed on GKE comes from GCR or Artifacts registry, and the deployment must be exposed to the public internet with a `public HTTP load balancer`.
## Prerequisites
- Linux system
- Terraform 
- Docker
## Getting Started
- To use this project, you will need to clone the repository to your local machine using the following command:
    
      git clone https://github.com/El-Zedy/GCP-Final-Project
- Then starting by creating infrastructure using terraform

      cd Terraform\ files
      terraform init
      terraform plan
      terrafrom apply
- Dockerizing application frontend and push it to GCR
      
      cd .. 
      cd Python\ app 
      docker build -t <frontend_app_image_name> .
      docker tag <frontend_app_image_name> gcr.io/<project_id>/<gcr_image_name>
      docker push gcr.io/<project_id>/<gcr_image_name>
- Pull redis image as application backend then push it to GCR
      
      docker pull redis
      docker tag redis gcr.io<project_id>/<gcr_image_name>
- Now ssh to private vm from your local

      gcloud compute ssh --zone "<zone_name>" "<privatevm_name>"  --tunnel-through-iap --project "<project_id>"
- prepare private vm to work with GKE
      
      sudo apt-get update
      sudo apt-get install kubectl  
      sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin
      gcloud container clusters get-credentials <privatecluster_name> --zone <zone_name> --project <project_id>
 - Creating kubernetes manifests files
      
        kubectl create ns <namespace_name>
      
      - create 2 services and deployments one for backend and another for forntend

      - hint: you can find all manifests files at `kubernetes manifests` directory
        
        
       kubectl create -f <frontendservice_name> -n <namespace_name>
       kubectl create -f <backendservice_name> -n <namespace_name>
       kubectl create -f <frontenddeployment_name> -n <namespace_name>
       kubectl create -f <backenddeployment_name> -n <namespace_name>
- Now our appliction up and running we can reach it using our frontend loadblacer
        
       kubectl get services -n <namespace_name> | greb frontendservice_name
       
- Hit `EXTERNAL_IP:8000` on your browser
- **Congratulation our web application is now available for users! :tada::tada:**

## Contributing

Contributions to this project are welcome and appreciated. To contribute, please follow these steps:

1. Fork the repository to your own account.
2. Create a new branch for your changes.
3. Make your changes and commit them to your branch.
4. Create a pull request to merge your changes into the main branch.
# Conclusion
In conclusion, the GCP-Final-Project repository serves as a demonstration of how to deploy a web application on GKE private cluster. By following the instructions in this repository, you can learn how build your infastrucure using terrafrom, Dockerizing your applications, push them as images to GCR, and deploy them to public users throw public loadblancer service on GKE private cluster.
- *hint:* Go to `screenshots` dirctory to double check and make sure that everything going right during your steps.
## Acknowledgements
We would like to acknowledge the following individuals and resources for their contributions to this project:

- Our instructor [Eng. Atef hares](https://github.com/atefhares), for providing guidance and support throughout the project.
- The Google Cloud Platform documentation, which served as a valuable resource for understanding the various GCP services and how they can be used together.
- The Terraform documentation, which provided helpful guidance on building iac on GCP.
- We would also like to thank our classmates and peers for their feedback and contributions to this project.

## Contact
If you have any questions or suggestions regarding this project, please contact the project owner at muhammadhassanelzedy@gmail.com .
