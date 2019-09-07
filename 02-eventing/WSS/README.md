# WebSocket Source

## Consumer Service

Deploy:

```
kubectl apply -f 000-ksvc.yaml
```

## The WebSocket Source

Connects to an service _outside_ of the cluster, therefore we need to apply some istio egress rules:

```
kubectl apply -f 020-egress.yaml
```

The source itself is a container, embedded in the `Kind: ContainerSource`, that can be parameterized. In ourcase the source sends all WebSocket events as CloudEvents, with the `eventType=my.custom.event`.  Later we will filter on exactly that type to route only these events to the consumer.

```
kubectl apply -f 021-source.yaml
```

## Routing of events

The trigger is used to route events, based on type etc to services, apply like:

```
kubectl apply -f 030-trigger.yaml
```

To view the subscriber's logs, execute:

```
kubectl logs --selector serving.knative.dev/service=event-display -c user-container
```
