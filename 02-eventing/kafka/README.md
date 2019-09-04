# Using Kafka as Eventing CHannel

The default installation of knative eventing uses an in-memory channel. This is fine for debugging purposes, but for production-ready applications it's recommended to use eventing implementations such as NATS or Kafka (which we will use in this example).

## Installing the Kafka Channel CRD

To use Kafka as a knative eventing channel, we first have to install the required CRDs (Custom Resource Definitions). You can do so by executing:

```
kubectl apply -f 00_kafka_crds/config
```

Afterwards, you can proceed with the examples located in this directory.