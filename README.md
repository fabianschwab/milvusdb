# Project Setup

Deploy a [Milvus Database](https://milvus.io) in **standalone** mode.

In the **standalone** deployment, Milvus operates as a single-node system which is most suitable for a MVP.
It is suitable for small-scale use cases where the volume of data and query requests is relatively low.
This mode is often used for development, testing, or scenarios where high availability and scalability are not critical.

1. OpenShift with the help of deployment files
2. Running locally for quick prototyping via docker or podman
3. Virtual Server Instance (VSI) directly installed or as docker container

For a MilvusDB there are some other services needed as well, which are also deployed:

1. [MinIO](https://min.io) is a high-performance, S3 compatible object store. It is built for large scale AI/ML, data lake and database workloads.
2. [etcd](https://etcd.io) is a distributed reliable key-value store.
3. Milvus itself, a vector database built for scalable similarity search.

Additionally:

- [Attu](https://github.com/zilliztech/attu) which is an all-in-one milvus administration tool.

## OpenShift Deployment

Everything can be deployed out of the box without any configuration,
but it is recommended to change the Milvus default username and password, as this service is available through the public internet.

Optional also the MinIO default username and password can be changed but isn't a must hence the service is only available within the cluster.

For a OpenShift deployment login to your cluster via `oc` cli.

```shell
# From the repositories root folder
cd openshift

# Create a new project
oc new-project milvus-db

# Or select a existing one where to deploy the resources
# oc project <project-name>

oc apply -f .
```

### Milvus configuration

This section explains how to change the default values like username, password or port for the Milvus database service.

TODO

### MinIO configuration

To change the default username and password for the MinIO service simple change the value of the environment variables of the deployment
and also in the Milvus deployment, so that the services are still able to talk to each other.

[minio.yaml](./openshift/minio.yaml)

```yaml
...
env:
    - name: MINIO_ACCESS_KEY
      value: <your-desired-username>
    - name: MINIO_SECRET_KEY
      value: <your-desired-password>
...
```

[milvus.yaml](./openshift/milvus.yaml)

```yaml
...
env:
    - name: ETCD_ENDPOINTS
      value: etcd-service:2379
    - name: MINIO_ADDRESS
      value: minio-service:9000
    - name: MINIO_ACCESS_KEY
      value: <your-desired-username>
    - name: MINIO_SECRET_KEY
      value: <your-desired-password>
...
```

## Milvus Cluster

In a clustered deployment, Milvus is set up in a distributed architecture, where multiple nodes work together as a cluster.
Clustering is designed for handling larger volumes of data and supporting more concurrent query requests.
It provides improved scalability and fault tolerance compared to standalone deployments.
Clusters are beneficial in production environments where high availability, reliability, and performance are essential.

TODO

## Locally

```shell
# From the repositories root folder
cd docker

podman compose up -d

#docker-compose up -d
```

Starts the 3 components necessary:

- MinIO Service
  - Console is available under [localhost:9001](http://localhost:9001)
  - API is available under [localhost:9000](http://localhost:9000)
- etcd Service
  - Runs on port `2379`
- Milvus Service
  - Available on port `19530`

And a GUI to connect to the database:

- Attu Service
  - UI is available under [localhost:3000](http://localhost:3000)

**Note** that "127.0.0.1" or "localhost" will not work when running Attu on Docker. Use your local IP address.
Through the `MILVUS_URL` environment variable, the url value in the UI on Attu can be prefilled.
In this case it's already set to the milvus service as it is name in the compose file.

## VSI in VPC

Todo

- [ ] Create vsi
- [ ] Option 1 with docker
- [ ] Option 2 install directly
- [ ] VPC and usage when used in a VPC which also hosts an OpenShift cluster
- [ ] Modify / change security group for internet access
