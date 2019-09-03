# Kubernetes Platform Events Source

## Consumer Service

Deploy:

```
k apply -f 000-ksvc.yaml
```

## The k8s Source

In order to be able to work with the platform events, we need to grant some rights:

```
k apply -f 010-serviceaccount.yaml
```

The source itself sends _all_ events to the broker. Later we will filter on exactly that time to route only these events to the consumer.

```
k apply -f 020-k8s-events.yaml
```

## Routing of events

The trigger is used to route events, based on type etc to services, apply like:

```
k apply -f 030-trigger.yaml
```

The above trigger uses no specific filter. Now, update the filter to route those events you like.
