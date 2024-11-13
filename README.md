

![arch (1)](https://github.com/user-attachments/assets/9f68f273-ab69-4d57-9aaa-02b4a24334e7)



# Monitoring and Logging Setup with Prometheus, Grafana, and EFK on Kubernetes

This guide walks through the steps to set up Prometheus, Grafana, and the EFK stack (Elasticsearch, Fluentd, and Kibana) in a Kubernetes environment. These tools are essential for monitoring cluster health, resource usage, and centralized log management.

## Overview

- **Prometheus:** An open-source systems monitoring and alerting toolkit. Prometheus scrapes metrics from configured endpoints and stores them in a time series database. It is widely used for monitoring Kubernetes clusters.

- **Grafana:** A multi-platform open-source analytics and interactive visualization web application. Grafana provides charts, graphs, and alerts for the web when connected to supported data sources such as Prometheus.

- **EFK Stack:** 
  - **Elasticsearch:** A search and analytics engine that stores and indexes log data.
  - **Fluentd:** An open-source data collector that unifies log data collection and consumption.
  - **Kibana:** An open-source data visualization dashboard for Elasticsearch, used to explore and visualize logs.

## Prerequisites

- A Kubernetes cluster
- kubectl installed and configured
- Helm installed on your local machine

## Step 1: Install Helm

Helm is a package manager for Kubernetes, which helps to install, update, and manage Kubernetes applications.

```bash
cd /usr/local/bin
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 777 get-helm.sh
./get-helm.sh
```




![1-helm-install](https://github.com/user-attachments/assets/6e17353e-655b-40e2-9867-180a1a690bf3)








## Step 2: Create a Namespace for Prometheus

Namespaces in Kubernetes allow you to partition your cluster's resources between multiple users.

```bash
kubectl create namespace prom
```




![2-create-ns](https://github.com/user-attachments/assets/251ec836-73e2-4849-af6f-1ffdd0081963)










## Step 3: Install Prometheus and Grafana

Add the Prometheus Helm repository and install the Prometheus-Grafana stack.

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```




![3-install-prometheus](https://github.com/user-attachments/assets/184185ac-5155-4f9b-8793-3ac14a69dc15)










```bash
helm install prometheus prometheus-community/kube-prometheus-stack -n prom
```









![3-install-prometheus1](https://github.com/user-attachments/assets/956d75ec-e609-4d78-9d55-fd4e609f0f48)

#####################################################################################



### Step 4: Verify Installation

Check the services to ensure Prometheus and Grafana are running.

```bash
kubectl get services -n prom
```



![4-verify-install](https://github.com/user-attachments/assets/433183b4-f11e-4a75-a797-337b85000a45)






### Step 5: Port Forwarding for Prometheus and Grafana

Access Prometheus and Grafana through port forwarding:

- **Prometheus:**

```bash
kubectl port-forward pod/prometheus-prometheus-kube-prometheus-prometheus-0 9090:9090 -n prom
```

- **Grafana:**

```bash
kubectl port-forward pod/prometheus-grafana 3000:3000 -n prom
```




# Access Prometheus:

Use the below link as above to access the Prometheus UI

http://PUBLIC-IP:9090





![image](https://github.com/user-attachments/assets/9f8f4466-6246-4f62-844a-d03515e32601)









![image](https://github.com/user-attachments/assets/76a8f397-4186-4386-9e26-bda849304c30)












####################################################################################################### 


Access Grafana by navigating to `http://localhost:3000` in your web browser. The default username and password are `admin/prom-operator`.

## Step 6: Configure Grafana

### Add Data Source

- In Grafana, navigate to `Configuration > Data Sources > Add Data Source`.
- Choose Prometheus as the data source.
- Set the URL to your Prometheus endpoint: `http://prometheus-kube-prometheus-prometheus.prom.svc.cluster.local:9090`.
- Save & Test the data source.






![image](https://github.com/user-attachments/assets/6cceaa41-fc4c-4021-a6e0-6b94d9e458e4)



















![image](https://github.com/user-attachments/assets/8964c96e-f9d2-41ab-a1a6-83453726bfe7)








###################################################################################




### Step 7: Create Dashboards

Create custom dashboards in Grafana:

- **Node Metrics Dashboard:** Monitor node health, resource utilization, etc.
- **Pod Metrics Dashboard:** Monitor pod-specific metrics.
- **Cluster Metrics Dashboard:** Overview of the entire cluster.

You can also import pre-built dashboards. For example, import dashboard ID `10000` to get started quickly.






![image](https://github.com/user-attachments/assets/2f7dab8c-7ec2-44d8-b404-fb39ca9457e1)













# Select the desired dashboard ID to add.

Considering K8s/Storage/Volumes/Namespace Dashboard:








![image](https://github.com/user-attachments/assets/38659a92-b27b-40bb-b350-03c456cb0038)










![image](https://github.com/user-attachments/assets/5721557d-0633-4288-84f8-2b7d869a6c31)















Copy the Id of K8s/Storage/Volumes/Namespace Dashboard i.e., 11455





# Import selected Dashboard in Grafana

Access Dashboard section & click on Import section.






![image](https://github.com/user-attachments/assets/9b8373ad-9563-4672-b576-ddf232b4e7f8)




















# Now enter the ID of the target new Dashboard i.e., 11455.













![image](https://github.com/user-attachments/assets/f8942e95-7103-4549-ae4a-d7188105e81a)














# Click on Load to load the new dashboard into Grafana.











![image](https://github.com/user-attachments/assets/d5ba42d5-1ce6-4196-a773-7c80c32b9401)













# Click on Import to import the new Dashboard & Access it.









![image](https://github.com/user-attachments/assets/c265dc48-20ff-4310-8b2a-56be33ea66ab)



















These steps allow us to easily integrate any dashboard from the Grafana library.

#################################################################################################



## Step 8: Deploy a Test Application

Deploy an application with multiple replicas to generate metrics.

```bash
kubectl create deployment test-app --image=nginx --replicas=6
```

Monitor the application in the Grafana dashboards you created.

## Step 9: Set Up Centralized Logging with EFK

To collect and manage logs centrally, we'll deploy the EFK stack.

### Step 9.1: Create a Namespace for Logging

```bash
kubectl create namespace kube-logging
```

### Step 9.2: Deploy Elasticsearch

Deploy Elasticsearch in the `kube-logging` namespace.

### Step 9.3: Deploy Kibana

Deploy Kibana in the `kube-logging` namespace.

### Step 9.4: Deploy Fluentd

Deploy Fluentd, which will collect and forward logs to Elasticsearch.

## Step 10: Access Kibana

Port forward the Kibana service and access it via a web browser.

```bash
kubectl port-forward service/kibana 5601:5601 -n kube-logging
```

Navigate to `http://localhost:5601` to access Kibana.

---
