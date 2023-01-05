+++
title = "Use Zap to Perform Dast"
description = ""
date = 2022-02-16T14:02:53+05:30
tags = ["owasp", "dast", "security", "tooling"]
categories = []
+++

- [Introduction](#introduction)
  - [What is Dynamic Application Security Testing (DAST)](#what-is-dynamic-application-security-testing-dast)
  - [What is ZAP](#what-is-zap)
- [How to use ZAP](#how-to-use-zap)
  - [ZAP Scan for API](#zap-scan-for-api)
    - [Local Run](#local-run)
      - [Example - for API with Swagger](#example---for-api-with-swagger)
      - [For SOAP/GraphQL](#for-soapgraphql)
    - [CircleCI Integrate](#circleci-integrate)
      - [Example - for API with Swagger](#example---for-api-with-swagger-1)
      - [For SOAP/GraphQL](#for-soapgraphql-1)
  - [ZAP Scan for Application (with UI)](#zap-scan-for-application-with-ui)
    - [Local Run for UI app](#local-run-for-ui-app)
    - [CircleCI Integration for UI app](#circleci-integration-for-ui-app)

## Introduction

### What is Dynamic Application Security Testing (DAST) 

Dynamic application security testing (DAST) is a process of testing an application or software product in an operating state. This kind of testing is helpful for industry-standard compliance and general security protections for evolving projects.

### What is ZAP

Zed Attack Proxy (ZAP) is a free, open-source penetration testing tool being maintained under the umbrella of the Open Web Application Security Project (OWASP). ZAP is designed specifically for testing web applications and is both flexible and extensible.

## How to use ZAP

### ZAP Scan for API

You can use [zap-api-scan](https://www.zaproxy.org/docs/docker/api-scan/) to perform scans against APIs defined by OpenAPI, SOAP, or GraphQL. If your API is protected with authentication, you will need to prepare a token or API key before running the script.

#### Local Run

##### Example - for API with Swagger

The following example shows how to run ZAP locally against an API with:
- url: http://192.168.1.10
- the swagger doc url:  http://192.168.1.10/swagger.json
- Authentication header name: Authorization
- Authentication header value: api_token_value

Steps:
1. Put your token or API key used for authentication into a configuration file or environment parameters, the following is a configuration file example.
```bash
ZAP_AUTH_HEADER_VALUE=api_token_value
ZAP_AUTH_HEADER=Authorization
ZAP_AUTH_HEADER_SITE=192.168.1.10
```
Note: the ZAP_AUTH_HEADER_SITE value should exclude the http/https protocol and port, e.g. for the target https://www.this-is-a-target.com:8080, the ZAP_AUTH_HEADER_SITE value should be www.this-is-a-target.com.

2. Run the following command to pull the zap docker image
```bash
docker pull owasp/zap2docker-stable
```

3. Run the following command to trigger zap-api-scan for an API with swagger doc (please replace the values in color when you use it for your API): 
```bash
docker run -it --env-file configuration_file_name --rm -v $PWD:/zap/wrk owasp/zap2docker-stable:latest zap-api-scan.py -t http://192.168.1.10/swagger.json -f openapi -r report.html
```

4. After executing the above commands, a report.html should be generated in your current directory, it contains the test results.


##### For SOAP/GraphQL
1. To run ZAP Scan against SOAP/GraphQL is very similar to the way to run it against Swagger API, the only difference is you need to change the "-f" option in the step 3 command to the following.

For SOAP:
```bash
docker run -it --env-file configuration_file_name --rm -v $PWD:/zap/wrk owasp/zap2docker-stable:latest zap-api-scan.py -t http://192.168.1.10/soap_wsdl_url -f soap -r report.html
```

For GraphQL:
```bash
docker run -it --env-file configuration_file_name --rm -v $PWD:/zap/wrk owasp/zap2docker-stable:latest zap-api-scan.py -t http://192.168.1.10/graphql_url -f graphql -r report.html
```


#### CircleCI Integrate

##### Example - for API with Swagger
The following example shows how to integrate ZAP into CircleCI to scan the API with:
- url: http://192.168.1.10
- the swagger doc url:  http://192.168.1.10/swagger.json
- Authentication header name: Authorization
- Authentication header value: api_token_value

Steps:
1. Add the following Environment Variables into your project env in the CircleCI
```bash
ZAP_AUTH_HEADER_VALUE=api_token_value
ZAP_AUTH_HEADER=Authorization
ZAP_AUTH_HEADER_SITE=target_url
```


Note: the ZAP_AUTH_HEADER_SITE value should exclude the http/https protocol and port, e.g. for the target https://www.this-is-a-target.com:8080, the ZAP_AUTH_HEADER_SITE value should be www.this-is-a-target.com.

2. Add the following content in .circleci/config.yml:
```yml
jobs:
  scan:
    docker:
      - image: owasp/zap2docker-stable
    steps:
      - run:
            command: |
              mkdir /zap/wrk
              zap-api-scan.py -f openapi -t https://target-url/swagger.json -r report.html
      - store_artifacts:
          path: /zap/wrk
          destination: zap-report
```


3. After executing the above commands, a zap-report/report.html should be generated in the pipeline artifacts, it contains the test results.

##### For SOAP/GraphQL
To integrate ZAP Scan into CI/CD to scan the SOAP/GraphQL API is very similar to the way to run it against Swagger API, the only difference is you need to change the "-f" option in the step 2 .circleci/config.yml file.

For SOAP:
```yaml
jobs:
  scan:
    docker:
      - image: owasp/zap2docker-stable
    steps:
      - run:
            command: |
              mkdir /zap/wrk
              zap-api-scan.py -f soap  -t https://target-url/soap_wsdl_url -r report.html
      - store_artifacts:
          path: /zap/wrk
          destination: zap-report
```

For GraphQL:

```yaml
jobs:
  scan:
    docker:
      - image: owasp/zap2docker-stable
    steps:
      - run:
            command: |
              mkdir /zap/wrk
              zap-api-scan.py -f graphql -t https://target-url/graphql_url -r report.html
      - store_artifacts:
          path: /zap/wrk
          destination: zap-report
```



### ZAP Scan for Application (with UI)
You can use [zap-full-scan](https://www.zaproxy.org/docs/docker/full-scan/) to perform a full active scan for a web application. If your application is protected with authentication, you will need to prepare an authorization header or cookie before running the script.

#### Local Run for UI app
The following example shows how to run ZAP locally against an application with:
- url: http://192.168.1.10
- Authentication header name: Authorization
- Authentication header value: authrozation_token_here

Note: Please change the header name to "Cookie" if your application is authenticated by cookie/session.

Steps:
1. Put your token or API key used for authentication into a configuration file or environment parameters, the following is a configuration file example.
```
ZAP_AUTH_HEADER_VALUE=authrozation_token_here
ZAP_AUTH_HEADER=Authorization
ZAP_AUTH_HEADER_SITE=192.168.1.10
```


Note: the ZAP_AUTH_HEADER_SITE value should exclude the http/https protocol and port, e.g. for the target https://www.this-is-a-target.com:8080, the ZAP_AUTH_HEADER_SITE value should be www.this-is-a-target.com.

2. Run the following command to pull the zap docker image
```
docker pull owasp/zap2docker-stable
```

3. Run the following command to trigger [zap-full-scan](https://www.zaproxy.org/docs/docker/full-scan/) (please replace the values in color when you use it for your application): 
```
docker run -it --env-file configuration_file_name --rm -v $PWD:/zap/wrk owasp/zap2docker-stable:latest zap-full-scan.py -t http://192.168.1.10 -r report.html
```

4. After executing the above commands, a report.html should be generated in your current directory, it contains the test results.

#### CircleCI Integration for UI app
The following example shows how to integrate ZAP into CircleCI to scan the application with:
- url: http://192.168.1.10
- Authentication header name: Authorization
- Authentication header value: authrozation_token_here

Steps:
1. Add the following Environment Variables into your project env in the CircleCI: 
```
ZAP_AUTH_HEADER_VALUE=api_token_value
ZAP_AUTH_HEADER=Authorization
ZAP_AUTH_HEADER_SITE=target_url
```


Note: the ZAP_AUTH_HEADER_SITE value should exclude the http/https protocol and port, e.g. for the target https://www.this-is-a-target.com:8080, the ZAP_AUTH_HEADER_SITE value should be www.this-is-a-target.com.

Note: Please change the header name to "Cookie" if your application is authenticated by cookie/session.

1. Add the following content in .circleci/config.yml:
```yaml
jobs:
  scan:
    docker:
      - image: owasp/zap2docker-stable
    steps:
      - run:
            command: |
              mkdir /zap/wrk
              zap-full-scan.py -t https://target-url -r report.html
      - store_artifacts:
          path: /zap/wrk
          destination: zap-report
```

3. After executing the above commands, a zap-report/report.html should be generated in the pipeline artifacts, it contains the test results.