# Blue/green deployments

## Deploying revision one (blue)

We’ll be deploying an image of a sample application that displays the text “App v1” on a blue background.

First, create a new file called blue-green-demo-config.yamland copy this into it:
```
kubectl apply -f 000-configuration-blue-green.yaml

configuration "blue-green-demo" configured
```

This will deploy the initial revision of the sample application. Before we can route traffic to this application we need to know the name of the initial revision which was just created. Using kubectl you can get it with the following command:

```
kubectl get configurations blue-green-demo -o=jsonpath='{.status.latestCreatedRevisionName}'
```

The result should look something like this:

The command above will return the name of the revision, it will be similar to `blue-green-demo-lcfrd`. In the rest of this document we will use this revision name, but yours will be different.

To route inbound traffic to it, we need to define a route. First, open the file `010-route.yaml`, and change the field `spec.traffic.revisionname` to match with the name of your revision.

```
kubectl apply -f 010-route.yaml

route "blue-green-demo" configured
```


Now we need to find the port and the IP address of the running istio ingress gateway, so run this:

```
INGRESSGATEWAY="$(minikube ip):$(kubectl get svc istio-ingressgateway --namespace istio-system --output 'jsonpath={.spec.ports[?(@.port==80)].nodePort}')"
echo $INGRESSGATEWAY
```

Now we get something like `192.168.39.25:31619`

You can now access the application via curl:

```
curl -H "Host: blue-green-demo.default.example.com" http://$INGRESSGATEWAY
```

## Deploying Revision 2 (Green)

Revision 2 of the sample application will display the text “App v2” on a green background. To create the new revision, we’ll edit our existing configuration in `000-configuration-blue-green.yaml` with an updated image and environment variables:

```
apiVersion: serving.knative.dev/v1alpha1
kind: Configuration
metadata:
  name: blue-green-demo # Configuration name is unchanged, since we're updating an existing Configuration
  namespace: default
spec:
  template:
    metadata:
      labels:
        knative.dev/type: container
    spec:
      containers:
        - image: gcr.io/knative-samples/knative-route-demo:green # URL to the new version of the sample app docker image
          env:
            - name: T_VERSION
              value: "green" # Updated value for the T_VERSION environment variable
```

Save the file, then apply the updated configuration to your cluster:

```
kubectl apply --filename blue-green-demo-config.yaml

configuration "blue-green-demo" configured
```

Find the name of the second revision with the following command:

```
kubectl get configurations blue-green-demo -o=jsonpath='{.status.latestCreatedRevisionName}'
```

In the rest of this document we will assume that the second revision is called blue-green-demo-m9548, however yours will differ. Make sure to use the correct name of the second revision in the manifests that follow.

At this point, the first revision (blue-green-demo-lcfrd) and the second revision (blue-green-demo-m9548) will both be deployed and running. We can update our existing route to create a new (test) endpoint for the second revision while still sending all other traffic to the first revision. Edit `010-route.yaml`:

```
apiVersion: serving.knative.dev/v1alpha1
kind: Route
metadata:
  name: blue-green-demo # Route name is unchanged, since we're updating an existing Route
  namespace: default
spec:
  traffic:
    - revisionName: blue-green-demo-lcfrd
      percent: 100 # All traffic still going to the first revision
    - revisionName: blue-green-demo-m9548
      percent: 0 # 0% of traffic routed to the second revision
      tag: v2 # A named route
```

Save the file, then apply the updated route to your cluster:

```
kubectl apply --filename 010-route.yaml

route "blue-green-demo" configured
```

At this point, no traffic will be directed to the new version yet. To change this, open `010-route.yaml` again, and change the percent values to your liking. After that, apply the configuration again, and you will see that the traffic will be distributed accordingly.
 