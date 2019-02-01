# Kubernetes Build

For running locally, install https://microk8s.io/docs/.
Otherwise, use a kubernetes provider (Google Cloud, OpenShift etc)

See docs/kubernetes

    # Start your local microk8s environment (you might prefer to use minikube)
    sudo microk8s.start
    sudo microk8s.status

    # Deploy open bank project
    sudo microk8s.kubectl create -f obp.yaml 
    # Output: 
    service/obpapi-service created
    deployment.apps/obp-deployment created
    service/postgres-service created
    deployment.apps/obp-postgres created
    persistentvolumeclaim/postgres created

# TODO

 - ConfigMap and Secrets management for connecting from obpapi-service <> postgres-service
 - Docker image & obpapi change to allow passing of postgress connection string via environment or file 
 - Minimal Docker containers for API Manager, API Explorer, API Tester to be also exposed as Services in
   the same way as chrisjsimpson/obp:minimal



## Docker only Build
If you just want run Open Bank project locally on your machine quickly, you can use this docker image

    # Build it
    docker build --no-cache --tag obpapi .
    # Or pull and run it 
    docker run -p 8080:8080 chrisjsimpson/obp:minimal

## Run 

    docker run -p 8080:8080 obpapi

    Visit http://127.0.0.1:8080/

## See also

- https://docs.docker.com/develop/develop-images/multistage-build/ 
- https://www.eclipse.org/jetty/documentation/9.4.x/maven-and-jetty.html
