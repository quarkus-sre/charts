# Quarkus SRE Workshop Helm Charts

## Overview

This repository contains Helm charts and deployment configurations for setting up a Quarkus-based microservices environment on OpenShift. The environment includes Kafka for streaming, Prometheus & Grafana for monitoring, and other essential services.

### Architecture Components

1. **Kafka (AMQ Streams)**: A distributed streaming platform for building real-time data pipelines. Used for messaging and data streaming between microservices.
  
2. **Prometheus**: An open-source systems monitoring and alerting toolkit. Collects metrics from configured targets at given intervals, evaluates rule expressions, and can trigger alerts if certain conditions are observed.
  
3. **Grafana**: An open-source platform for monitoring and observability. Used for visualizing Prometheus metrics.

4. **Jaeger**: An end-to-end distributed tracing tool for monitoring and troubleshooting transactions in complex, distributed systems.

5. **KEDA (Kubernetes Event-Driven Autoscaling)**: Allows for advanced autoscaling, including event-driven scaling.

6. **Red Hat OpenShift Pipelines**: Provides a Kubernetes-native CI/CD solution based on Tekton, which is part of the CD Foundation.

### Microservices

1. **Order Processor**: Manages incoming orders and routes them for processing.
  
2. **Inventory**: Manages stock levels of products.
  
3. **Label Generator**: Generates shipping labels.
  
4. **UPS Label API**: Manages the UPS-specific aspects of label generation.
  
5. **Fedex Label API**: Manages the Fedex-specific aspects of label generation.

### Namespaces

1. **quarkus-dev**: Houses the Quarkus-based microservices.
  
2. **kafka-streaming**: Dedicated to Kafka components, including brokers and topics.
  
3. **kafka-logging**: Used for monitoring components like Prometheus and Grafana.
  
4. **keda**: Houses KEDA components for event-driven autoscaling.

## Prerequisites

- OpenShift Cluster (OCP)
- Helm 3.x
- OpenShift CLI (`oc`)

## Quick Start
### Add Helm Repository
First, add the Quarkus SRE Helm repository:

```bash
helm repo add quarkus-sre https://quarkus-sre.github.io/charts
```
### Create Namespaces
1. Quarkus Development: Houses the Quarkus microservices.
```bash
oc new-project quarkus-dev
```
2. Kafka Streaming: For Kafka clusters and topics.
```bash
oc new-project kafka-streaming
```
3. Monitoring (Prometheus & Grafana): For logging and monitoring.
```bash
oc new-project kafka-logging
```
4. KEDA: For Kubernetes-based event-driven autoscaling.
```bash
oc new-project keda
```
### Install Operators
Install the following operators from the OpenShift OperatorHub:
- Red Hat Integration AMQ Streams
- Prometheus Operator
- Red Hat OpenShift distributed tracing platform
- KEDA
- Red Hat OpenShift Pipelines

## Install Helm Charts
Navigate to the charts directory to install these charts:

1. AMQ Streams
```bash
helm template -f amqstreams/values-cluster-with-metrics.yaml amqstreams | oc apply -f-
```
2. Prometheus
```bash
helm template -f prometheus/values.yaml prometheus | oc apply -f-
```
3. Grafana
```bash
helm template -f grafana/values.yaml grafana | oc apply -f-
```
- Configure the Prometheus data source manually in Grafana.
- Import Grafana dashboards:
  - Strimzi Kafka Exporter Dashboard (grafana-dashboards/kafka-exporter.json)
  - Quarkus SRE Dashboard (grafana-dashboards/sre-quarkus.json)
4. Jaeger
```bash
helm template -f jaeger/values.yaml jaeger | oc apply -f-
```
5. KEDA
```bash
helm template -f keda/values.yaml keda | oc apply -f-
```
## Deploy Microservices on Quarkus-Dev
Deploy the following Quarkus microservices using S2I (Source-to-Image):

1. Order Processor
```bash
oc new-app openshift/java:openjdk-11-ubi8~https://github.com/quarkus-sre/order-processor --name=order-processor -n quarkus-dev
```
2. Inventory
```bash
oc new-app openshift/java:openjdk-11-ubi8~https://github.com/quarkus-sre/inventory --name=inventory -n quarkus-dev
```
3. Label Generator
```bash
oc new-app openshift/java:openjdk-11-ubi8~https://github.com/quarkus-sre/label-generator --name=label-generator -n quarkus-dev
```
4. UPS Label API
```bash
oc new-app openshift/java:openjdk-11-ubi8~https://github.com/quarkus-sre/ups-label-api --name=ups-label-api -n quarkus-dev
```
5. Fedex Label API
```bash
oc new-app openshift/java:openjdk-11-ubi8~https://github.com/quarkus-sre/fedex-label-api --name=fedex-label-api -n quarkus-dev
```

6. Order Processor Client
```bash
oc new-app https://github.com/quarkus-sre/order-processor-client -n quarkus-dev
```

## Apply Monitoring & Autoscaling Configurations

1. Pod Monitors
```bash
cd yamls
oc apply -f pod-monitors/
```
2. Prometheus Rules for SLOs
```bash
oc apply -f prometheus-rules-slos/
```
3. KEDA AutoScaler
```bash
oc apply -f keda-auto-scaler/label-generator-scaled-object.yaml
```
## Final Environment Layout
- Kafka: kafka-streaming
- Microservices: quarkus-dev
- Monitoring (Prometheus and Grafana): kafka-logging
- Jaeger: openshift-distributed-tracing
- KEDA: keda
