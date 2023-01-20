# Custom error pages for  OCP 4.9 - 4.12
https://docs.openshift.com/container-platform/4.10/networking/ingress-operator.html

## Customizing HAProxy error code response pages

Is it possible to customize 503 and 404 error codes

- 503
  - returned when the application pod is not running
- 404
  - returned when the requested route does not exists

Custom error codes must be specified in a config map.

<b>The files name must be exactly as follow:</b>

- error-page-503.http
- error-page-404.http

## Custom error code format

Custom error code pages must follow the [HAproxy HTTP error page configuration guidelines](https://www.haproxy.com/documentation/hapee/latest/configuration/config-sections/http-errors/)


## Overall procedure 

1) Create the config map

```
oc -n openshift-config create configmap custom-error-code-pages --from-file=error-page-404.http --from-file=error-page-503.http
```

2) Patch the ingress-controller-operator

```
oc patch -n openshift-ingress-operator ingresscontroller/default --patch '{"spec":{"httpErrorCodePages":{"name":"custom-error-code-pages"}}}' --type=merge
```

- Wait for the ingress cluster operator to stop progressing

## Check HAproxy custom error pages existence

- ```oc -n openshift-ingress rsh <router_pod> cat /var/lib/haproxy/conf/error_code_pages/error-page-503.http```
- ```oc -n openshift-ingress rsh <router_pod> cat /var/lib/haproxy/conf/error_code_pages/error-page-404.http```



## Tests

-  ```oc new-project test-ingress```
-  ```oc new-app django-psql-example```

### 503 error code test

- ```stop all the application pod```
- ```curl -vk <route_hostname>```

### 404 error code test

- ```visit a non existing route in the browser```
- ```curl -vk <non-existing-route>```

