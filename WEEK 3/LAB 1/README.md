# Lab 1 : GCP

## Compute Engine

### Tutorials

[Quickstart using a Linux VM](https://cloud.google.com/compute/docs/quickstart-linux "Quickstart using a Linux VM")

[Connect to Linux VMs](https://cloud.google.com/compute/docs/instances/connecting-to-instance "Connect to Linux VMs")

[Connecting to Linux VMs using advanced methods](https://cloud.google.com/compute/docs/instances/connecting-advanced "Connecting to Linux VMs using advanced methods")

[Using startup scripts on Linux VMs](https://cloud.google.com/compute/docs/instances/startup-scripts/linux "Using startup scripts on Linux VMs")

[Querying VM metadata](https://cloud.google.com/compute/docs/metadata/querying-metadata "Querying VM metadata")

## gcloud

### Understanding commands

```bash
# The gcloud command format.
gcloud + release level (optional) + component + entity + operation + positional args + flags
```

```bash
# Example gcloud command for creating a virtual machine instance.
gcloud compute instances create <instance_name> --zone=<zone>
```

## Running commands

```bash
# Get the gcloud help information.
gcloud help
```

```bash
# Get the gcloud cheat sheet.
gcloud cheat-sheet
```

```bash
# Get projects help information.
gcloud projects --help
```

```bash
# Get projects create help information.
gcloud projects create --help
```

```bash
# Create a new project and set as default.
gcloud projects create <project_id> --name=<project_name> --set-as-default
```

```bash
# List the available billing accounts.
gcloud beta billing accounts list
```

```bash
# Link a billing account with the project.
gcloud beta billing projects link <project_id> --billing-account=<account_id>
```

```bash
# List the available services, filter on the name 'compute', and echo the result. 
SERVICE=$(gcloud services list --available --filter="NAME ~ ^compute" --format="value(NAME)")
echo $SERVICE
```

```bash
# Enable the Compute Engine API.
gcloud services enable $SERVICE
```

```bash
# Set the preferred Compute Engine region.
gcloud config set compute/region <region>
```

```bash
# Set the preferred Compute Engine zone.
gcloud config set compute/zone <zone>
```

```bash
# List the current configuration.
gcloud config list
```

```bash
# List the current networks.
gcloud compute networks list
```

```bash
# List the current firewall rules.
gcloud compute firewall-rules list
```

```bash
# Delete the firewall rules.
gcloud compute firewall-rules delete default-allow-icmp default-allow-internal default-allow-rdp default-allow-ssh
```

```bash
# Delete the default network.
gcloud compute networks delete default
```

```bash
# Create a custom mode network.
gcloud compute networks create vpc-network --subnet-mode=custom
```

```bash
# Create a default subnet.
gcloud compute networks subnets create default --network=vpc-network --range=10.0.1.0/24
```

```bash
# Add the external IP from the Cloud Shell session to a variable, add the CIDR notation, and echo the result.
SHELL_EXTERNAL_IP=$(curl ifconfig.co)
SHELL_EXTERNAL_IP="$SHELL_EXTERNAL_IP""/32"
echo $SHELL_EXTERNAL_IP
```

```bash
# Create a firewall rule for SSH traffic.
gcloud compute firewall-rules create allow-ssh-ingress \
  --network=vpc-network \
  --priority=1000 \
  --direction=INGRESS \
  --action=ALLOW \
  --target-tags=ssh \
  --source-ranges=$SHELL_EXTERNAL_IP \
  --rules=tcp:22
```

```bash
# Create a firewall rule for web traffic.
gcloud compute firewall-rules create allow-web-ingress \
  --network=vpc-network\
  --priority=1000 \
  --direction=INGRESS \
  --action=ALLOW \
  --target-tags=web \
  --source-ranges=0.0.0.0/0 \
  --rules=tcp:80,tcp:443 
```

```bash
# Create a service account.
gcloud iam service-accounts create web-instance-01 --display-name=web-instance-01
```

```bash
# Add the email address of the service account to a variable and echo the result.
SERVICE_ACCOUNT=$(gcloud iam service-accounts list --filter="email ~ ^web-instance-01" --format="value(email)")
echo $SERVICE_ACCOUNT
```

```bash
# Create the instance, including a startup script.
gcloud compute instances create web-instance-01 \
  --machine-type=e2-micro \
  --description="Web server" \
  --image-project=ubuntu-os-cloud \
  --image-family=ubuntu-2004-lts \
  --network=vpc-network \
  --subnet=default \
  --service-account=$SERVICE_ACCOUNT \
  --tags=ssh,web \
  --metadata=organization="CloudSkills.io" \
  --metadata-from-file=startup-script="$HOME/Linux-Cloud-Fundamentals/Week 3/LAB 1/startup-script.sh"
```

```bash
# Connect to the instance over SSH.
gcloud beta compute ssh web-instance-01
```

```bash
# Verify that the Apache HTTP Server service is active (running).
systemctl status apache2.service
```

```bash
# Verify the custom index.html file.
cat /var/www/html/index.html
```

```bash
# Close the SSH connection.
exit
```

```bash
# Add the external IP from the instance to a variable, add the HTTP protocol, and echo the result.
HTTP_ADDRESS=$(gcloud compute instances describe web-instance-01 --format="value(networkInterfaces[0].accessConfigs[0].natIP)")
HTTP_ADDRESS="http://""$HTTP_ADDRESS"
echo $HTTP_ADDRESS
```

```bash
# Verify if the instance is reachable
curl $HTTP_ADDRESS
```

```bash
# Stop the instance.
gcloud compute instances stop web-instance-01
```

## More information

[Free Trial and Free Tier](https://cloud.google.com/free "Free Trial and Free Tier")

[Compute Engine documentation](https://cloud.google.com/compute/docs "Compute Engine documentation")

[Cloud Shell documentation](https://cloud.google.com/shell/docs "Cloud Shell documentation")

[Cloud SDK documentation](https://cloud.google.com/sdk/docs "Cloud SDK documentation")

[The gcloud tool overview](https://cloud.google.com/sdk/gcloud "The gcloud tool overview")

[The gcloud cheat sheet](https://cloud.google.com/sdk/docs/cheatsheet "The gcloud cheat sheet")

[The gcloud cheat sheet: Understanding commands](https://cloud.google.com/sdk/docs/cheatsheet#understanding_commands "The gcloud cheat sheet: Understanding commands")


ANSWERS TO LAB 1

I ran most of the commands but exceeded the billing quota for the free tier in GCP and couldn't continue. Also, I completed 23 commands out of 31 commands given.

Attached is the screenshot of the error message gotten from the billing quota.




# Lab 2: Manage Packages and Services on a Linux VM (Azure or AWS)

1. Create a Linux VM
2. Install the Apache Web Server
3. Start the service status via command line
4. Investigate the service status via command line
5. Stop the service 

Challenge: Create a boostrapping script that will install and start this service on new EC2 VMs

### Notes:

Install and Configure Apache (Ubuntu)
* https://ubuntu.com/tutorials/install-and-configure-apache#1-overview

How to install Apache on RHEL 8 / CentOS 8 Linux
* https://linuxconfig.org/installing-apache-on-linux-redhat-8

How to use cloud-init to customize a Linux virtual machine in Azure on first boot
* https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-automate-vm-deployment

Create bootstrap actions to install additional software
* https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-bootstrap.html


LAB 2 ANSWERS

Question 1: I have created a Linux Virtual Machine using Azure instead of AWS.

Quesion 2: I have installed the Apache web server.

Question 3: I started the service via a command line.

Question 4: I investigated the service via a command line.

Question 5: I successfully stopped the service.

Challenge: Create a boostrapping script that will install and start this service on new EC2 VMs ------ ( I have successfully craeted a bootstrapping script that was used to install and start a service on new EC2 VM). Attached are the screenshots.