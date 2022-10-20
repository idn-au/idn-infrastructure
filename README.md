# IDN Infrastructure Planning
This repo will act as a reference for resources relating to the IDN's infrastructure, as well as for infracode use (Kubernetes deployment files, etc).

## Infrasucture Requirements

### Deployments

- Docker
    - [Prez](https://github.com/RDFLib/prez)
    - Olis
    - [Jena-Fuseki](https://jena.apache.org/documentation/fuseki2/)
- Static sites
    - [IDN project website](https://github.com/idn-au/dev.idnau.org)
    - [Metadata form](https://github.com/idn-au/idn-metadata-creator)

### Storage

- Hot storage
- Cold storage (potentially petabytes)

### Environments

- Dev
- Test
- Prod

## MRC Resourcing

### [Machine flavours](https://docs.cloud.unimelb.edu.au/guides/allocations/)

Flavour name       | vCPUs | RAM (GB) |	Suggested use
-------------------|-------|----------|---------------
uom.general.1c4g   | 1     | 4        |	Simple web hosting
uom.general.2c8g   | 2     | 8        | Database driven website
uom.general.4c16g  | 4     | 16       |	Data Science using RStudio or JupyterHub
uom.general.8c32g  | 8     | 32       |	Data science on larger data sets
uom.general.12c48g | 12    | 48       |
uom.general.16c64g | 16    | 64       |

### Container Orchestration
MRC/Nectar/OpenStack supports container orchestration (like Kubernetes) using OpenStack Magnum.

### Storage

- Hot storage - Object storage - OpenStack Swift
- Cold storage - Mediaflux/AWS S3 Glacier


## Kubernetes
Helpful resources:
- [Kubernetes documentation](https://kubernetes.io/docs/home/)
- [Phippy goes to the zoo](https://www.cncf.io/phippy/phippy-goes-to-the-zoo-book/)
    - A children's book designed to explain kubernetes in simple terms
- [kubectl](https://kubernetes.io/docs/reference/kubectl/)
    - Documentation for the Kubernetes CLI tool `kubectl`, which is used to interact with clusters

Plugins to potentially use to enhance the cluster(s):
- [nginx-ingress](https://docs.nginx.com/nginx-ingress-controller/)
    - For using [Ingresses](https://kubernetes.io/docs/concepts/services-networking/ingress/) to manage internal traffic within the cluster
- [Linkerd](https://linkerd.io/2.12/overview/)
    - A service mesh to monitor resources within the cluster
- [cert-manager](https://cert-manager.io/docs/)
    - Automatically manages certificates within the cluster

There are two proposed options so far to manage separate environments (dev, test & prod) using Kubernetes:
- 3 separate clusters, one for each environment, or
- 2 separate clusters, with each environment on a separate namespace
    - The first (dev) cluster will act as a dev environment only for cluster-level config, and will be far less resourced compared to the non-dev cluster
    - For the non-dev cluster, each environemnt will be given a [namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) for separation

### 3 clusters, 1 per environment

<table>
<tr>
<th rowspan="2">Cluster</th>
<th rowspan="2">Namespace</th>
<th colspan="4">Deployments</th>
<th colspan="3">Resources</th>
</tr>
<tr>
<th>Name</th>
<th>RAM (GB)</th>
<th>vCPU</th>
<th>Volume (GB)</th>
<th>RAM (GB)</th>
<th>vCPU</th>
<th>Volume (GB)</th>
</tr>
<tr>
<td rowspan="3">Dev</td>
<td rowspan="3">default</td>
<td>Prez</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
<td rowspan="3">8</td>
<td rowspan="3">0.5</td>
<td rowspan="3">20</td>
</tr>
<tr>
<td>Olis</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Jena-Fuseki</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td rowspan="3">Test</td>
<td rowspan="3">default</td>
<td>Prez</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
<td rowspan="3">8</td>
<td rowspan="3">0.5</td>
<td rowspan="3">20</td>
</tr>
<tr>
<td>Olis</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Jena-Fuseki</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td rowspan="3">Prod</td>
<td rowspan="3">default</td>
<td>Prez</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
<td rowspan="3">8</td>
<td rowspan="3">0.5</td>
<td rowspan="3">20</td>
</tr>
<tr>
<td>Olis</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Jena-Fuseki</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
</table>

### 2 Clusters (1 dev 1 non-dev), 1 namespace per environment for non-dev cluster

<table>
<tr>
<th rowspan="2">Cluster</th>
<th rowspan="2">Namespace</th>
<th colspan="4">Deployments</th>
<th colspan="3">Resources</th>
</tr>
<tr>
<th>Name</th>
<th>RAM (GB)</th>
<th>vCPU</th>
<th>Volume (GB)</th>
<th>RAM (GB)</th>
<th>vCPU</th>
<th>Volume (GB)</th>
</tr>
<tr>
<td rowspan="3">Dev-infra</td>
<td rowspan="3">default</td>
<td>Prez</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
<td rowspan="3">8</td>
<td rowspan="3">0.5</td>
<td rowspan="3">20</td>
</tr>
<tr>
<td>Olis</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Jena-Fuseki</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td rowspan="9">Non-dev</td>
<td rowspan="3">dev</td>
<td>Prez</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
<td rowspan="9">8</td>
<td rowspan="9">0.5</td>
<td rowspan="9">20</td>
</tr>
<tr>
<td>Olis</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Jena-Fuseki</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td rowspan="3">test</td>
<td>Prez</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Olis</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Jena-Fuseki</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td rowspan="3">prod</td>
<td>Prez</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Olis</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
<tr>
<td>Jena-Fuseki</td>
<td>4-8</td>
<td>0.5-1</td>
<td>20</td>
</tr>
</table>

