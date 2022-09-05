# Helm Charts

Helm charts for Quarkus SRE

# Add this repo

helm repo add quarkus-sre https://quarkus-sre.github.io/charts/
## Env:

1. Running OCP
2. Install Operators: 
   1. Red Hat Integration AMQ Streams
   2. Red Hat OpenShift distributed tracing platform
   3. Red Hat OpenShift Pipelines
   4. Prometheus Operator
3. Install Charts (base path ```charts``` directory):
   1. AMQ ```helm template -f amqstreams/values-cluster-with-metrics.yaml amqstreams | oc apply -f-```
   2. Prometheus ```helm template -f prometheus/values.yaml prometheus | oc apply -f-```
   3. Grafana ```helm template -f grafana/values.yaml grafana | oc apply -f-```
      1. Configure the prometheus datasource manually on Grafana
      2. Import the Strimzi Kafka Exporter Dashboard (grafana-dashboards/kafka-exporter.json)
      3. Import the Quarkus SRE Dashboard (grafana-dashboards/sre-quarkus.json)
   4. Jaeger ```helm template -f jaeger/values.yaml jaeger | oc apply -f-```
4. Install Microservices (TODO pipeline. Pipeline using chart)
   1. Order Processor (https://github.com/quarkus-sre/order-processor)
   2. Inventory (https://github.com/quarkus-sre/inventory)
   3. Label Generator (https://github.com/quarkus-sre/label-generator)
   4. UPS Label API (https://github.com/quarkus-sre/ups-label-api)
   5. Fedex Label API (https://github.com/quarkus-sre/fedex-label-api)
   6. Working using s2i without pipeline
5. Apply Application's Pod Monitors
   1. ```cd yamls```
   2. ```oc apply -f pod-monitors/inventory-pod-monitor.yaml```
   3. ```oc apply -f pod-monitors/label-generator-pod-monitor.yaml```
   4. ```oc apply -f pod-monitors/order-processor-pod-monitor.yaml```
6. Apply Application's SLOs Prometheus Rules
   1. ```cd yamls/slos```
   2. ```oc apply -f prometheus-rules-slos/prometheus-rules-availability-slos.yaml```
   3. ```oc apply -f prometheus-rules-slos/prometheus-rules-label-fedex-latency-slos.yaml```
   4. ```oc apply -f prometheus-rules-slos/prometheus-rules-label-ups-latency-slos.yaml```

## Final Environment

Kafka: kafka-streaming
Microservices: quarkus-dev
Prometheus and Grafana: kafka-logging
Jaeger: openshift-distributed-tracing