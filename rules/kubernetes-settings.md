We will run MongoDB as a separate pod for each microservice.  When creating the Kubernetes manifest, use the image ```mongodb/mongodb-community-server:latest```.

When creating Kubernetes manifests, please follow these rules.
- Use the namespace kasbench.
- Create each service with one pod and allow it to scale to a maximum of 100 pods.
- Give each pod 100 millicores and 0.5GB of RAM.
