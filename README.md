# Nxgenhub-Challenge
**Deploy a simple "Hello World" web server in Kubernetes**

<u>OBJECTIVE</u> 

Deploy a Web Server that prints “Hello, World!” using the specified technologies and best practices. 

<u>TECHNOLOGIES REQUIRED</u>

1. Terraform 
2. Kubernetes
3. Helm
4. Ingress Controller
5. Load Balancer 
6. Amazon Web Services (AWS)
7. Git
8. Docker

<u>STEPS</u>

1. Kubernetes Cluster Setup in AWS with Terraform

    i. Connect to your AWS account with the following command: `aws configure` and enter your access/secret access keys.
                            
    ii. Follow the commands to setup a k8s cluster in the official terraform github page here: https://developer.hashicorp.com/terraform/tutorials/kubernetes/eks

    iii. Once the cluster is up, install kubectl tool in your local machine here: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

    iv. Run the following command to retrieve the access credentials for your cluster and configure kubectl: `aws eks --region $(terraform output -raw region) update-kubeconfig \
    --name $(terraform output -raw cluster_name)` Thereafter, run `kubectl get nodes` to verify all nodes are running and in ready state. Create a namespace for the ingress controller at this time: `kubectl create ns traefik`

2. Deploy a web server that serves the text `Hello World`

    i. Connect your local computer with dockerhub by the command `docker login`.
    Ensure you have Docker Desktop installed in your local machine. Push a simple docker image (your webserver) with the command `docker push <repository/imagename:tag` to your remote repository.

    ii. Install Helm by running the script here: https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

    iii. Create the web server helm chart: `helm create webserver`. Edit the values.yaml file with the image name, port, clusterip service labels, selectors and port.

    iv. Package the helm chart with `helm package webserver` and then deploy the package with `helm install web-app ./webserver-0.1.0.tgz`

3. Ingress Configuration

    i. Follow the instructions here to install Traefik with helm: "helm repo add traefik https://traefik.github.io/charts using the `helm repo add`, `helm repo update` and `helm install` commands.

    ii. Check the traefik namespace to confirm the Loadbalancer service is running and externalIP is assigned.

    iii. Create the ingress manifest file and deploy in the same namespace with the web server application. Check to confirm that the Loadbalancer externalIP is registered with the ingress as an `ADDRESS`.

    iv. In Route53 in AWS, create a hosted zone and an A record for your DNS. Ensure you capture your Loadbalancer DNS address as the alias to the A record. 

    v. Enter your web address on your browser to confirm the web server is reachable.

4. Common Operational And Administrative Tasks
    
    i. We can carry out auto and manual scaling of resources. For auto scaling, we can do for example:
`kubectl autoscale deployment helloworld-deployment --cpu-percent=70 --min=1 --max=10`. Threshold adjustments such as  (--cpu-percent) and min/max pod counts as per workload requirements are also possible. For manual scaling, we can do `kubectl scale deployment helloworld-deployment --replicas=x`
(replace x with the desired number of replicas.)

    ii. Logging solutions like Elasticsearch, Fluentd, and Kibana EFK stack can be implemented to check logs.


    iii. metrics collection using the four golden signals: Latency, Error, Traffic, Saturation can be scraped with Prometheus and visualized with Grafana.

iv. Other areas we can look at are Pod status, events, CPU, and memory.
`kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl top pods
kubectl top nodes`





