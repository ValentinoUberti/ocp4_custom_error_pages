# Docs OCP 4.10 
https://docs.openshift.com/container-platform/4.10/networking/ingress-operator.html

## Customizing HAProxy error code response pages

Is it possible to customize 503 and 404 error codes

- 503
  - returned when the application pod is not running
- 404
  - returned when the requested route does not exists

Custom error codes must be specified in a config map with the following keys:

- error-page-503.http
- error-page-404.http

## Custom error code format

Custom error code pages must follow the [HAproxy HTTP error page configuration guidelines](https://www.haproxy.com/documentation/hapee/latest/configuration/config-sections/http-errors/)


## Overall procedure 

1) Create the config map

```
oc -n openshift-config create configmap custom-error-code-pages --from-file=error-page-503.http --from-file=error-page-404.http
```

2) Patch the ingress-controller-operator

```
oc patch -n openshift-ingress-operator ingresscontroller/default --patch '{"spec":{"httpErrorCodePages":{"name":"custom-error-code-pages"}}}' --type=merge
```

