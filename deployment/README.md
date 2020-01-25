# Deployment steps

### The deployment folder description::
•	Jenkinsfile has the pipeline as a code script that automates the SDLC starting from cloning the repo until deploying and testing the application. Below are the main steps executed in the pipeline file:

    •	lone the repo from GitHub
    •	ackage the app as a war to deploy it as a container
    •	aunch application tests
    •	ockerize the applications (UMSL, Sonar, Prometheus and Grafana). Then push them to Amazon ECR repo
    •	eploy the containers to Amazon Kubernetes EKS cluster using EKS
    •	Analyze code quality using SonarQube
•	UMSL directory: has all the deployment files used by HELM, which are:

    •	hart.yaml the main Helm chart file that has some basic info about the chart like the version.
    •	alues.yaml has all the environment variables used in the deployment such as image URLs, ports, names.
    •	emplates directory: has all the YAML files used to deploy the Pods and services, the files in the templates folder are:
    
            -	deployment.yaml deploys 1 replica of the main UMSL application
            -	service.yaml exposes the application port to be accessed externally, and the service type is Load Balancer to create a new AWS ELB and use it to access the app externally.
            -	sonar.yaml has both service and deployment blocks to deploy and expose the pod using NodePort service type to use the same worker node host along with a specific port to access the app. The container port is 9000, and the service port is: 31234
            -	prometheus.yaml has both service and deployment blocks to deploy and expose the pod using NodePort service type to use the same worker node host along with a specific port to access the app. The container port is 9090, and the service port is: 31244
            -	grafana.yaml has both service and deployment blocks to deploy and expose the pod using NodePort service type to use the same worker node host along with a specific port to access the app. The container port is 3000, and the service port is: 31254


•	The main UMSL app is accessed using the ELB DNS name, while the other 3 pods mentioned above (sonar, Grafana and Prometheus) are accessed using the Node IP address along with the speicifed port as shown below:

UMSL App:
http://ab5c68ff63ec611eab0a202d17123605-272060125.us-east-1.elb.amazonaws.com:8081/services/UMSL
Sonar:
http://ec2-54-90-224-38.compute-1.amazonaws.com:31234
Prometheus:
http://ec2-54-90-224-38.compute-1.amazonaws.com:31244
Grafana:
http://ec2-54-90-224-38.compute-1.amazonaws.com:31254



