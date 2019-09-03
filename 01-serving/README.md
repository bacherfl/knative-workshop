# Knative Serving

Install one of the services with 

```
kubectl apply -f 010_ksvc_ghello.yaml
```

# Accessing the service

We need to find the port and the IP address of the running istio ingress gateway, so run this:

```
INGRESSGATEWAY=istio-ingressgateway
echo $(minikube ip):$(kubectl get svc $INGRESSGATEWAY --namespace istio-system --output 'jsonpath={.spec.ports[?(@.port==80)].nodePort}')
```

Now we get something like `192.168.39.25:31619`

Next we need to get the URL of the service, with `k get ksvc`, like:

```
NAME            URL                                        LATESTCREATED         LATESTREADY           READY   REASON
helloworld-go   http://helloworld-go.default.example.com   helloworld-go-pzbw7   helloworld-go-pzbw7   True 
```

To access the service, run:

```
curl -H "Host: helloworld-go.default.example.com" 192.168.39.25:31619
```

> NOTE: the Host header matches the URL from the above printout.

After 2 minutes of idle time, the services scales to zero, running the `curl...` again brings the service back
