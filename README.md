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

## Run it in integration cloud:
### Backend services must be deployed in cloud (for example GCP)
1) create an instance in cloud
2) create a firewall rule
3) copy the Hospital-Service-JDK11-2.0.0.jar in the cloud instance
4) download JDK in the cloud instance: with the command: curl -L https://download.java.net/java/GA/jdk9/9.0.4/binaries/openjdk-9.0.4_linux-x64_bin.tar.gz -O
5) run the service

## Show other features
### debug and test
### connectors store

