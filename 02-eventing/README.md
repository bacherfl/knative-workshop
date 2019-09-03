# Setting up Eventing

The two samples in here use the default broker, which needs to be injected like:


```
kubectl label namespace default knative-eventing-injection=enabled
```

> NOTE: this is required for all eventing samples in this sub dir.

## Simple Examples

The class contains two examples, working with the ContainerSource and the k8s event source.

* k8s
* WSS

## Advanced samples

If time allows, feel free to use some of [these examples](), that are specific to Apache Kafka

* Kafka

