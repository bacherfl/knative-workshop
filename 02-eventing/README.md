# Setting up Eventing

The two samples in here use the default broker, which needs to be injected like:


```
kubectl label namespace default knative-eventing-injection=enabled
```

> NOTE: this is required for all eventing samples in this sub dir.

