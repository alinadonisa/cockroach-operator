# cockroach-operator
Kubernetes Operator for CockroachDB

This project is not production ready and is in an alpha state.

## Create an openshift cluster on GCP

1. Login to openshift https://cloud.redhat.com/openshift/
2. Press "Create Cluster" button--> Red Hat Openshift Container platform-->GCP--> Installer provision infrastructure
3. Folow the instruction and download the binary for openshift-install and openshift-client, unzip them and register to the PATH environment var:

For macos:
```bash
curl -LO https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-install-mac.tar.gz
curl -LO https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-mac.tar.gz

tar -xzf openshift-install-mac.tar.gz 
tar -xzf openshift-client-mac.tar.gz 

//Make binary executable
chmod +x ./openshift-install
chmod +x ./oc

//Move the binary into your PATH
sudo mv ./openshift-install /usr/local/bin/openshift-install
sudo mv ./oc /usr/local/bin/oc

//check install
openshift-install version
oc version
```

For Linux:
```bash
//Download binary
curl -LO https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-install-linux.tar.gz
curl -LO https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

tar -xzf openshift-install-linux.tar.gz 
tar -xzf openshift-client-linux.tar.gz 

//Make binary executable
chmod +x ./openshift-install
chmod +x ./oc

//Move the binary into your PATH
sudo mv ./openshift-install /usr/local/bin/openshift-install
sudo mv ./oc /usr/local/bin/oc

//check install
openshift-install version
oc version
```
4. The installer will use the default configuration, so set the PROJECT on the default configuration

```bash
gcloud auth application-default login
```

5. Enable following api services:
```bash
gcloud services enable compute.googleapis.com
gcloud services enable cloudapis.googleapis.com 
gcloud services enable cloudresourcemanager.googleapis.com 
gcloud services enable dns.googleapis.com 
gcloud services enable iam.googleapis.com 
gcloud services enable iamcredentials.googleapis.com 
gcloud services enable servicemanagement.googleapis.com
gcloud services enable serviceusage.googleapis.com
gcloud services enable storage-api.googleapis.com 
gcloud services enable storage-component.googleapis.com
gcloud services enable cloudbilling.googleapis.com 
```
7. Enable billing for our project
8. Create an service account and attach it to the project:
```bash
gcloud iam service-accounts create open-shift-sa
gcloud projects add-iam-policy-bindings openshift-sandbox --member  "serviceAccount:open-shift-sa@openshift-sandbox-id.iam.gserviceaccount.com" --role "roles/owner"
``` 
9. Set credentials:
```bash
mkdir -p ~/.gcp
gcloud iam service-accounts keys create ~/.gsp/osServiceAccount.json --iam-account open-shift-sa@openshift-sandbox-id.iam.gserviceaccount.com 
```
10. Create a folder where the in instalation of the cluster will be saved

```bash
mkdir ~/oshift
```
11. Make sure you have a DNS domain. Delegate a subdomain to google cloud
```bash
gcloud dns managed-zones openshift-domain
```

Add the server names from this command in the DNS provider zone configuration file
```bash
gcloud dns managed-zones describe openshift-domain
```

Check that the records match using this commands:
```bash
gcloud dns managed-zones describe openshift-domain
dig @8.8.8.8 mydomain.com NS +short
```
12. Create the cluster. Follow the instruction from comand line. 
```bash
 openshift-install create cluster --dir ~/oshift --log-level=debug
```
13. Plesae do not delete the folder where the installation was made, in our case ~/oshift. If you delete this you will have to decomission manually the infrastructure from GCP.

14. If you want to delete the cluster run this cmd to decommision infrastructure on GCP:
```bash
 openshift-install destroy cluster --dir ~/oshift --log-level=debug
```
 To remove the cluster from redhat list you have to go to Clusters menu from Redhat, select the cluster, press Actions and choose Archive cluster option.

 