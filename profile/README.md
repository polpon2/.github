# Late-Day Tokens Marketplace

Welcome to the Late-Day Tokens Marketplace project, a microservices-based e-commerce app for handling orders, payments, inventory, and delivery. This project focuses on managing transactions across various services, exploring scalability, testing, and monitoring.

## Overview

The Late-Day Tokens Marketplace is a marketplace specifically designed for late-day tokens. The project involves building a straightforward e-commerce app using a microservices architecture to handle orders, payments, inventory, and delivery. Although a frontend is not mandatory, rigorous testing of the application is required. The testing code should be maintained in a separate Git repository.

For simplicity, the user service is not included in this project. It is assumed that the user specified during order creation is legitimate. Requests should contain instructions on potential failure scenarios during order processing across services, and mechanisms for handling and reverting these failures appropriately. OpenTelemetry is used to collect the required logs across all services.

## Project Conditions

### Payment Service Conditions

The payment service operates under the following conditions:

- New users will have some initial credits.
- If the user exists, credits will be deducted based on the price of the purchase.
- Possible outcomes of the payment service include SUCCESS, INSUFFICIENT_FUND, TIMEOUT, and UNKNOWN.
- If a step is reverted, the funds should be returned to the user account.

## Key Features

- **Microservices Architecture**: The project is built using a microservices architecture to handle distinct functionalities seamlessly.
- **Thorough Testing**: The application undergoes comprehensive testing to ensure robustness and reliability. The testing code is maintained in a separate Git repository.
- **Failure Scenario Handling**: Clear instructions and mechanisms are provided for handling potential failure scenarios during order processing across services.
- **OpenTelemetry Integration**: OpenTelemetry is used to collect logs across all services, facilitating effective monitoring and debugging.

&nbsp;

# Deployment Guide

This guide outlines the deployment process for the Late-token project on a Kubernetes cluster. We will start by deploying Signoz & RabbitMQ and then proceed with other components.

## Prerequisites

Before you begin, ensure that you have the following prerequisites in place:

- A functioning Kubernetes cluster.
- `kubectl` command-line tool properly configured to work with your cluster. (or equivalent)
- You have [traefik](https://doc.traefik.io/traefik/getting-started/install-traefik/) installed and running, https://doc.traefik.io/traefik/getting-started/install-traefik/
- [Optional] You have [signoz](https://signoz.io/docs/install/kubernetes/others/#install-signoz-on-kubernetes-with-helm) installed follow this guide, https://signoz.io/docs/install/kubernetes/others/#install-signoz-on-kubernetes-with-helm

&nbsp;

## Cloning the Deployment Repository

Begin by cloning the Token deployment repository and organizing the file tree as follows:

```bash
# Clone the deployment repository
git clone https://github.com/polpon2/sales-late-day-tokens-deployment.git

# Move into the cloned repository
cd sales-late-day-tokens-deployment

# Your file tree should look like this:
#
# sales-late-day-tokens-deployment
# ├── rabbitmq
# │   ├── cluster-operator.yml
# │   └── rabbitmq.yaml
# ├── README.md
# ├── db-deployment.yaml
# ├── db-service.yaml
# ├── dockerconfigjson.yaml (create this, instruction below)
# ├── pvc.yaml
# ├── route.yaml
# ├── token-backend-deployment.yaml
# ├── token-backend-service.yaml
# ├── token-dead-letter-deployment.yaml
# ├── token-dead-letter-service.yaml
# ├── token-delivery-deployment.yaml
# ├── token-delivery-service.yaml
# ├── token-inventory-deployment.yaml
# ├── token-inventory-service.yaml
# ├── token-manager-deployment.yaml
# ├── token-manager-service.yaml
# ├── token-order-deployment.yaml
# ├── token-order-service.yaml
# ├── token-payment-deployment.yaml
# └── token-payment-service.yaml
```
&nbsp;

## RabbitMQ Deployment

Firstly, let's deploy RabbitMQ:

```bash
# Assuming you are in the 'sales-late-day-tokens-deployment' folder

# Start rabbitmq cluster
kubectl apply -f rabbitmq/cluster-operator.yml

# Then create the cluster object named 'rabbit-mq'
kubectl apply -f rabbitmq/rabbitmq.yaml
```

&nbsp;

## Secret Configuration
Before you could pull the image from the github package, you first need a correct secret configuration for k8s.

https://dev.to/asizikov/using-github-container-registry-with-kubernetes-38fb

#### TDLR:
create & apply the secret out right
```bash
kubectl create secret docker-registry dockerconfigjson-github-com --docker-server=ghcr.io --docker-username={Your username} --docker-password={Your docker PAT (personal access token)}
```
OR create the config file so we could apply it later
```bash
kubectl create secret docker-registry dockerconfigjson-github-com --docker-server=ghcr.io --docker-username={Your username} --docker-password={Your docker PAT (personal access token)} --dry-run=client --output=yaml > ghsecret.yaml
```

&nbsp;

## Apply the rest of the services and deployment
Make sure [traefik](https://doc.traefik.io/traefik/getting-started/install-traefik/) is installed and running

```bash
# Assuming you are in the 'sales-late-day-tokens-deployment' folder
kubectl apply -f .
```

Check if all the processes is pulled successfully
```bash
watch -n 1 kubectl get all
```

Should take ~30secs - 1min for all services to start, then voila. Late Token sales backend is now running, with swagger at http://localhost/docs

&nbsp;

## Deleting Deployments
Deleting Services
```bash
# Assuming you are in the 'sales-late-day-tokens-deployment' folder
kubectl delete -f .
```

Deleting the Rabbitmq Cluster Object and Cluster itself
```bash
# Delete cluster object
kubectl delete rabbitmqclusters.rabbitmq.com rabbit-mq

# Delete cluster
kubectl delete -f rabbitmq/cluster-operator.yml
```

Stop/Star Signoz Cluster

https://signoz.io/docs/operate/kubernetes/#stopstart-signoz-cluster

```bash
# To stop SigNoz cluster:
helm -n platform uninstall "my-release"

# To start/resume SigNoz cluster:
helm -n platform install "my-release"
```

&nbsp;

# Testing Guide

`sales-late-day-tokens-test` contains tests for the sales late-day tokens functionality.

## Prerequisite

Make sure you have [Poetry](https://python-poetry.org/) installed.

## Disclaimer

1. It is recommended to have a fresh database before running the tests (to avoid user conflicts with the tester)
2. Make sure no one request requests while the test is running, or else some test will break
3. It is recommended to test in development settings (since it WILL change stuff in the database for real)

## Running Tests
```bash
# Assuming you are inside the "sales-late-day-tokens-test" folder

poetry install && poetry run pytest tests
```

## Test Repo Structure
```bash
sales-late-day-tokens-test
├── README.md
├── app.py
├── poetry.lock
├── pyproject.toml
└── tests # Tests Module
    ├── __init__.py
    ├── test_core.py # Tests normal functionality
    ├── test_fail.py # Tests forced kill
    └── test_timeout.py # Tests forced timeout
```

# Contributing
Feel free to contribute by opening issues or creating pull requests.