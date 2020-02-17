# DemoEI_Main
main functionalities of WSO2 EI
## Prerequisites
1) WSO2 Integration Studio
2) WSO2 EI 6.6.0 or 7.0
3) backend services (...)
4) JDK 8 +
5) (optionnal) WSO2 Integration Cloud account
6) (optional) docker runtime
7) (optional) ActiveMQ

## Pre tasks
1) download backend service and run it:
  java -jar Hospital-Service-JDK11-2.0.0.jar
2) import project SampleService and SampleServiceCompositeApplication
3) Optional: run on Kubernetes (GCP)

3a - Create a GCP cluster (2 nodes, small size)

3b -  Install Nginx Ingress Controller:

kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin --user stene1969@gmail.com

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.29.0/deploy/static/mandatory.yaml

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.29.0/deploy/static/provider/cloud-generic.yaml

3c - Install EI Operator:
kubectl create -f deploy/service_account.yaml

kubectl create -f deploy/role.yaml

kubectl create -f deploy/role_binding.yaml

kubectl create -f deploy/crds/integration_v1alpha1_integration_crd.yaml

kubectl create -f deploy/operator.yaml

kubectl apply -f deploy/config_map.yaml

3d update file /etc/hosts with the following row: <INGRESS IP>  wso2ei

## Use Case 1: Routing
### Create the following 3 Endpoints:
GrandOakEP: http://localhost:9090/grandoaks/categories/{uri.var.category}/reserve

set http enpoints and have POST as method.

### Create new API (HealthcareAPI)
name: HealthcareAPI; context:/healthcare;

API resource property:Uri style: URI_TEMPLATE; Uri-template:/categories/{category}/reserve; method: POST

### Add the following mediators:
1) Property (name: Hospital; value expression: json-eval($.hospital)
1b) Property (name: card_number; value expression: json-eval($.cardNo)
2) Switch Mediator (Num of branches: 3; Source Xpath: get-property('Hospital'); regex1: grand oak community hospital; regex2: clemency medical center; regex3: pine valley community hospital
3)Log mediator in each branch (CUSTOM; Value/Expression: fn:concat('Routing to ', get-property('Hospital'))
4) Send Mediator in each branch with the corresponding EP
5) Log Mediator in the default branch (CUSTOM; Value/Expression: fn:concat('Invalid hospital - ', get-property('Hospital'))
6) Respond Mediator in the default branch
7) Add Send Mediator in the Output sequence

### Test the use case
Run the script: runDemoEI_Main_routing.sh

## Use Case 2: Data Transformation
### add data Data Mapper
use input.json and output.json as template file
### Test the use case
Run the script: runDemoEI_Main_transf.sh
## Use Case 3: Service Chain
### Update HealthcareAPI accordingly
1) remove the Send Mediator and replace it with a Call mediator; same endpoint
2) add the ServiceChain sequence at the end of the process
3) add the following Property before the Call Mediator: name: uri.var.hospital; type: LITERAL; value: grandoaks
### Test the use case
Run the script: runDemoEI_Main_transf.sh

## Deploy in integration cloud:
### Backend services must be deployed in cloud (for example GCP)
1) create an instance in cloud
2) create a firewall rule
3) copy the Hospital-Service-JDK11-2.0.0.jar in the cloud instance
4) download JDK in the cloud instance: with the command: curl -L https://download.java.net/java/GA/jdk9/9.0.4/binaries/openjdk-9.0.4_linux-x64_bin.tar.gz -O and unzip with tar xvf openjdk-9*_bin.tar.gz
5) run the backend service in the GCP instance
6)Deploy the artifact to WSO2 Integration Cloud; copy the url
7) update the url in runDemoEI_Main_transf.sh
### Test the use case
Run the script: runDemoEI_Main_transf.sh

## Deploy in Kubernetes / Create and run docker image
### create Kubernetes Project from the Integration Studio (Generate Kubernetes Artifacts)
1) use any name for the prject and Integration Name; as target repository name use stene1969/demoei (for example; use lower case char only); tag: 1.0.0
2) run the docker container, for example: docker run -d -p 8290:8290 stene1969/demoei
3) create a secret in K8s to access Docker repository: kubectl create secret docker-registry stene1969 --docker-username=stene1969 --docker-password=<pwd> --docker-email=stefano@wso2.com
4) update the generated integration-cr.yaml file (in the workspace of Integration Studio): add the following row under spec: imagePullSecret: stene1969
5) create the ingress: kubectl create -f eiingress.yaml
  
### execute the test:  
curl -k -v -X POST --data @requestTransf.json https://wso2ei/healthcare/categories/surgery/reserve --header "Content-Type:application/json"


## Show other features
### debug and test
### connectors store

