# AMQ Streams Operator Helm Deploy

AMQ Streams Helm Chart customizes and deploys the AMQ Streams Operator from the Red Hat Integration team.

## Installing the chart

To install the chart:

```bash
$ helm template -f amqstreams-operator/values.yaml amqstreams-operator | oc apply -f-
```

If you have already created a cluster-wide subscription to the AMQ Streams Operator in OperatorHub, run:

```bash
$ helm template -f amqstreams-operator/values-subscribed.yaml amqstreams-operator | oc apply -f -
```

The Kafka CRD is deployed, and the Kafka cluster resources are spun up by the operator.

**If `.Values.kafka.generateCerts` is set to false, you must ensure that you have cluster and client certificates stored as Secrets in the deployment namespace, with the appropriate labels, in order for the deployment to be successful. The Secrets the operator will search for will be named:**

- `[cluster-name]-cluster-ca (Cluster Key)`
- `[cluster-name]-cluster-ca-cert (Cluster Cert)`
- `[cluster-name]-clients-ca (Clients Key)`
- `[cluster-name]-clients-ca-cert (Clients Cert)`

All of the secrets must carry the following labels, as well:

- `strimzi.io/kind=Kafka`
- `strimzi.io/cluster=[cluster-name]`
