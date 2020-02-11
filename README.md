# DemoEI_Main
main functionalities of WSO2 EI
## Prerequisites
1) WSO2 Integration Studio
2) WSO2 EI 6.6.0 or 7.0
3) backend services (...)
4) JDK 8 +
4) (optionnal) WSO2 Integration Cloud account
5) (optional) docker runtime

## Pre tasks
1) download backend service and run it:
  java -jar Hospital-Service-JDK11-2.0.0.jar
2)


## Use Case 1: Routing
### Create a new project: DemoServices
### Create the following 3 Endpoints:
GrandOakEP: http://localhost:9090/grandoaks/categories/{uri.var.category}/reserve

ClemencyEP:  http://localhost:9090/clemency/categories/{uri.var.category}/reserve

PineValleyEP:  http://localhost:9090/pinevalley/categories/{uri.var.category}/reserve

all are http enpoints and have POST as method.

### Create new API (HealthcareAPI)
name: HealthcareAPI; context:/healthcare; Save Location: DemoServices

API resource property:Uri style: URI_TEMPLATE; Uri-template:/categories/{category}/reserve; method: POST
