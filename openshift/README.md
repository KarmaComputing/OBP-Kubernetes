# Deploy OpenBankProject on Openshift


## Openshift hosted cluster

- [How to login to openshift using the `oc` CLI](#login-to-openshift-using-oc-cli)
- [Deploy OBP-API to your OpenShift Cluster](#deploy-obp-api-to-your-openshift-cluster)
- [Deploy OBP API to your local development environment](#deploy-obp-api-to-your-local-development-environment)


### Login to Openshift using `oc` CLI

Objective: When you type "`oc get pods`" you get back some or no pods. If your cluster is new, you will see "`No resources found in <username> namespace`"
For that to work, configure your terminal to use `oc` CLI against your Openshift cluster. The UI it not intuitive at all so here's the instructions:

1. Login to your web cluster to get your authentication key (e.g. onsole-openshift-console.apps.sandbox-m2.abc123.p1.openshiftapps.com)
2. Click the '?' then "Command line tools", then "Copy login command"

> Verbose cli login details: To log in using the CLI, collect your token from the web console’s Command Line page, which is accessed from Command Line Tools in the Help menu. The token is hidden, so you must click the copy to clipboard button at the end of the oc login line on the Command Line Tools page, then paste the copied contents to show the token. [Official docs](https://docs.openshift.com/container-platform/3.11/cli_reference/get_started_cli.html#cli-reference-get-started-cli)

### Errors: random uuid

tldr: 

1. Fix containers uuid handling using [this example](https://github.com/chrisjsimpson/obp-kubernetes/blob/openshiftcompatibility/entrypoint.sh#L1-L13).
2. See [fully working obp-api openshift container](index.docker.io/chrisjsimpson/obpapi-kube) example
3. Historical context see: [Building Non Root Docker Images OpenShift](https://blog.karmacomputing.co.uk/building-non-root-docker-images-openshift/), and [Openshift will not run your container as a root user](https://number1.co.za/openshift-will-not-run-your-container-as-a-root-user/)


Detail:

The current OBP-API docker images will not run on Openshift deployed custers. An example image which does is available at:
[dockerhub](index.docker.io/chrisjsimpson/obpapi-kube), and the [code reference which handles the random uid scenario in OpenShift clusters](https://github.com/chrisjsimpson/obp-kubernetes/blob/openshiftcompatibility/entrypoint.sh#L1-L13).



```
 OBP openshift ATM  Postgress curl (time sink: cluster registry permissions/access) undocumented use of generate-jetty-start.sh in unknown repo, perhaps refers to image: index.docker.io/tawoe/obp-api however the tags are undocumented (tag "hw" exists and is most recently modified but no information, "lastest" tag is 9 days go) Neither will run on a production Openshift cluster chrisjsimpson/obpapi-kube will.


********************************************************************
WARNING: User is 1012560000
         The user should be (re)set to 'jetty' in the Dockerfile
********************************************************************
/generate-jetty-start.sh: 10: cannot create /var/lib/jetty/jetty.start: Permission denied
jetty dry run failed:
```

# Deploy OBP-API to your OpenShift Cluster

1. Ensure your secrets are configured as intended (see `obp.yaml`)
2. Apply the OBP manifest(s) to your k8s cluster

A quickstart valid OBP-API deployment manifest is provided: 

```
oc apply -f obp.yaml
```

Validate:

```
oc get pods
```


# Deploy OBP API to your local development environment

Tools required:

- `crc` ([Download & install crc](https://github.com/code-ready/crc/releases))


Start `crc`

```
crc setup
crc start
```

Enable podman:

> This sets-up podman to 'speak' to your local openshift cluster *rather* than your host machine.

```
eval $(crc podman-env)
```


> **Warning**
> If you see "error did not resolve to an alias and no unqualified-search registries are defined"
> Then edit `/etc/containers/registries.conf` and add/uncomment to your prefered registry e.g. `'unqualified-search-registries = ["docker.io"]` [ref: podman no longer searched dockerhub error](https://unix.stackexchange.com/questions/701784/podman-no-longer-searches-dockerhub-error-short-name-did-not-resolve-to-an))


### Clone OBP-API & build `obp-api` image


> **Warning**
> Work in progress. This clone url is subject to change to the [official repo](https://github.com/OpenBankProject/OBP-API.git)

```
git clone https://github.com/KarmaComputing/OBP-API.git
cd OBP-API
```
