Openshift-image-registry
===========


OpenShift Container Platform can build images from your source code, deploy them, and manage their lifecycle. It provides an internal, integrated container image registry that can be deployed in your OpenShift Container Platform environment to locally manage images. Below is a step-by-step guide on how to use it:



------------

1. Login to OpenShift Cluster


```
    oc login <openshift_cluster_url>
```    


2. Get the Image Registry URL

To push and pull images, you'll need the URL of the OpenShift Image Registry. Run this command to get the registry URL:

```
    oc get route -n openshift-image-registry
```    

The output will show the registry URL, usually in the format: default-route-openshift-image-registry.apps.<cluster_domain>

3. Login to the Image Registry

Use podman to interact with the OpenShift Image Registry.

```
    podman login -u $(oc whoami) -p $(oc whoami -t) <registry_url>
```    


3. Tag and Push a Local Image

Once authenticated, you can tag a local image with the registry URL and push it to OpenShift:


```
    podman tag <local_image_name> <registry_url>/<project>/<image_name>:<tag>
```    
```
    e.g., podman tag my-app-image default-route-openshift-image-registry.apps.example.com/my-project/my-app-image:latest
``` 

Then, push the image to the registry:

```
    podman push <registry_url>/<project>/<image_name>:<tag>
```    
```
    e.g., podman push default-route-openshift-image-registry.apps.example.com/my-project/my-app-image:latest
``` 

3. Use an Image from the Registry in a Deployment

To deploy an application using an image from the registry, you can reference the image in a deployment configuration:


```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    name: my-app
    spec:
    replicas: 1
    selector:
        matchLabels:
        app: my-app
    template:
        metadata:
        labels:
            app: my-app
        spec:
        containers:
        - name: my-app
            image: <registry_url>/<project>/<image_name>:<tag>
            ports:
            - containerPort: 8080
```
```
    image: default-route-openshift-image-registry.apps.example.com/my-project/my-app-image:latest
```

