# Snappcloud-hub

This repostiroy provides needed configurations for the Snappcloud-hub catalog source and its subscriptions. This catalog is generated and maintained using the [catalog tamplate](https://olm.operatorframework.io/docs/tasks/creating-a-catalog/#catalog-creation-using-catalog-templates) method.

## Usage

### Prerequisites

- opm v1.23.1+

### Preparing the index

First you should prepare the index of the catalog source regarding your operator either it's new or already cataloged.

#### Add a new operator

In order to add a new operator to the catalog source, first make sure that the latest operator docker image and its bundle are pushed to a docker registry. Then follow these steps:

1. Consider your operator name as `<OPERATOR-NAME>`
2. Create a template file in `templates/<OPERATOR-NAME>.yaml` directory
3. Fulfill it with data like this and modify it regarding the operator docker registry uri and its bundled versions.

    ```yaml
    GenerateMajorChannels: true
    GenerateMinorChannels: false
    Stable:
    Bundles:
    - Image: repository-uri/<OPERATOR-NAME>-bundle:v0.8.9
    - Image: repository-uri/<OPERATOR-NAME>-bundle:v0.9.0
    ```

4. Render the template:

    ```bash
    $ opm alpha render-veneer semver -o yaml < templates/<OPERATOR-NAME>.yaml > catalog/<OPERATOR-NAME>/index.yaml
    ```

5. Validate the catalog:

    ```bash
    $ opm validate catalog
    ```

## Upgrade an operator

To upgrade an already-cataloged operator, follow these steps:

1. Modify the operator template file in `templates/<OPERATOR-NAME>.yaml` directory and add the new bundle version to your desired channel in the template. (i.e. candidate, fast, stable)

2. Render the template:

    ```bash
    $ opm alpha render-veneer semver -o yaml < templates/<OPERATOR-NAME>.yaml > catalog/<OPERATOR-NAME>/index.yaml
    ```

3. Validate the catalog:

    ```bash
    $ opm validate catalog
    ```

### Catalog Docker image

Now build the catalog docker image:

```bash
$ docker build -t <registry-uri>/snappcloud-hub-catalog:latest
```

And push it:

```bash
$ docker push <registry-uri>/snappcloud-hub-catalog:latest
```

### Apply the catalog source

The catalog source must be applied on the Kubernetes cluster if haven't been done already:

```bash
kubectl apply -f catalog-source.yaml
```

### Subscription

Create a subscription file for your operator and apply it on the cluster:

```bash
kubectl apply -f s3-operator-subcscription.yaml
```

