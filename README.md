# Knative demos on Minikub

Details on Knative tutorial

## What is needed

* Minikube installation

## Start the system

Run the `setup.sh` script, to start the installation of 

* Minikube
* Strimzi (Apache Kafka)
* Knative Serving and Eventing

## Error handling

If one of the install steps fails (e.g. Istio / Serving), e.g.:

```
error: unable to recognize "STDIN": no matches for kind "Image" in version "caching.internal.knative.dev/v1alpha1"
```

just apply the failing part _AND_ rest of the script again...
