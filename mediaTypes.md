# mediaTypes & artifactTypes

Artifact tools will need the ability to query their specific artifacts from a registry. In addition, registry vendors will want to understand the different artifact types they present to their users, providing additional information about the artifact. Including, the ability to launch the associated tool.


## Media Types
The current OCI spec expresses mediaTypes define the persistance of an **OCI Image**. In this proposal, mediaTypes are more generic, expressing the hierarchy of persistance, regardless of the specific type. By decoupling persistance from the type, a registry may persist the artifact without needing to know the artifactType. As long as the artifact follows the validated structure of MedaiTypes, it can be persisted. 

The renamed, more generic OCI mediaTypes have `image` removed:

| MediaType | Usage |
|-|-|
|`application/vnd.oci.index.v1+json` | OCI Index |
|`application/vnd.oci.manifest.v1+json` | OCI Artifact Manifest |
|`application/vnd.oci.layer.v1.tar+gzip` | OCI Layer |

## ArtifactTypes

ArtifactTypes represent a specific instance of an OCI Manifest and the optional OCI Index. 

By separating the artifactType from mediaType, registries can utilize OCI persistance formating, while expressing the type for various tools, or listing the types in a registry listing.

ArtifactTypes are required additional elements.

To provide for user friendly names, a list of media types and short/friendly names will be maintained. A link to the associated tools or info about the artifact will also be provided. 

| ArtifactType | Display Name | Info | 
|-|-|-|
|`application/vnd.oci.image.index.v1` | OCI Multi-Arch Image | [Docker](https://www.docker.com/products/docker-desktop) *|
|`application/vnd.oci.image.manifest.v1` | OCI Image | [Docker](https://www.docker.com/products/docker-desktop) * |
|`application/vnd.cncf.helm.manifest.v3` | Helm | [Helm](https://helm.sh)
|`application/vnd.deislabs.cnab.manifest.v1` | CNAB | [Duffle](https://cnab.io), [Docker-application](https://www.docker.com/search/node?keys=docker-application)|
|`application/vnd.microsoft.arm.manifest.v1`| ARM | [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) |
|`application/vnd.aws.ecs.task.manifest.v1`| ECS Task | [aws Task](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)|

*\* most registry providers automatically convert `oci.image` manifests to the format requested by the client.*

### Adding new artifacts
By separating artifactTypes from mediaTypes, new artifacts can be easily added. Artifact owners can simply submit a PR to a central clearing house, or the OCI Spec. Registry authors can periodically ingest this list, allowing them to display and reason over the new types. As long as the artifact type utilizes the existing mediaTypes, supporting a new type is straight forward for registry owners. 

**ArtifactType string formatting:**

`application/vnd/`[vendor | working group]`/`[product | project]`/`[index | manifest]`/`[version]


### Layer ArtifactTypes
Registries typically show the top level objects, represented by `mediaType: application/vnd.oci.index.v1+json` | `application/vnd.oci.manifest.v1+json`

With the decoupling of persistance from type, additional `artifactTypes` are required to express what each layer represents.

Each artifact implementation MUST define the artifactTypes they require. The meaning of these layer ArtifactTypes are only meaningful to tools that understand the top level artifactType.

For illustrative purposes, Image, Helm and CNAB will be expressed below. 

**Note:** While MUST will be included in the Helm and CNAB examples, these are just examples of what these specs *MAY* look like. The purpose of this proposal is to provide groups the freedom to define their unique artifact representation using the generic OCI MediaTypes.

## OCI Image Index
The image index is a higher-level manifest which points to specific image manifests, ideal for one or more platforms. 

```json
"mediaType": "application/vnd.oci.index.v1+json",
"artifactType": "application/vnd.oci.image.index.v1"
```
**OCI Image Index example**

`docker run demo42.azurecr.io/samples/images/wordpress:0.1.0`
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.index.v1+json",
  "artifactType": "application/vnd.oci.image.index.v1",
  "manifests": [
    {
      "mediaType": "application/vnd.oci.manifest.v1+json",
      "artifactType": "application/vnd.oci.image.manifest.v1",
      "size": 4288,
      "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.oci.manifest.v1+json",
      "artifactType": "application/vnd.oci.image.manifest.v1",
      "size": 4288,
      "digest": "sha256:33c5d954c76495826126178ded2113d1ada0a1ea70f95b65f866312462c6e957",
      "platform": {
        "architecture": "arm",
        "os": "linux",
        "variant": "v5"
      }
    },
```

**OCI Image Manifest example**

```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.manifest.v1+json",
  "artifactType": "application/vnd.oci.image.manifest.v1",
  "config": {
    "mediaType": "application/vnd.oci.config.v1+json",
    "artifactType": "application/vnd.oci.image.config.v1",
    "size": 16078,
    "digest": "sha256:bd698aa18aa02a2f083292b9448130a3afaa9a3e5fba24fc0aef7845c336b8ad"
  },
  "layers": [
    {
      "mediaType": "application/vnd.oci.layer.v1.tar+gzip",
      "artifactType": "application/vnd.oci.image.layer.v1",
      "size": 23133155,
      "digest": "sha256:9a1a13172ed974323f7c35153e8b23b8fa1c85355b6b26cc3127e640e45ef0aa"
    },
    ...
```
> See [/container-image/readme.md](./container-image/readme.md) for more details of the decoupled proposal.

## Helm Chart
> **Note:** *this is just an illustrative example of the power of decoupling artifactTypes from mediaTypes.*

> The persistance format has yet to be decided by the Helm community, the following represents what a Helm chart *could* be expressed as.

Unlike OCI Image manifests, Helm charts use layers to represent different elements of a chart. The files that make up the chart are separated from the values file *(parameter values)*. By separating values, multiple deployments of the same chart, only differentiated by the values, may be tracked and evaluated by new Helm tools.

```json
"mediaType": "application/vnd.oci.manifest.v1+json",
"artifactType": "application/vnd.cncf.helm.manifest.v3"
```
> See [/helm/readme.md](./helm/readme.md) for more details of the decoupled proposal.

**Helm manifest example**

`helm install demo42.azurecr.io/samples/helm/wordpress:0.1.0`

```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.manifest.v1+json",
  "artifactType": "application/vnd.cncf.helm.v3",
  "config": {
    "mediaType": "application/vnd.oci.config.v1+json",
    "artifactType": "application/vnd.cncf.helm.config.v3",
    "size": 7023,
    "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7",
    "annotations": {
      "org.opencontainers.title": "wordpress",
      "org.opencontainers.version": "0.1.0"
    },
    "manifests": [
      {
        "mediaType": "application/vnd.oci.manifest.v1+json",
        "artifactType": "application/vnd.cncf.helm.chart.v3",
        "size": 4288,
        "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
      },
      {
        "mediaType": "application/vnd.oci.manifest.v1+json",
        "artifactType": "application/vnd.cncf.helm.values.v3",
        "size": 323,
        "digest": "sha256:4a2f7cd2e53307d7e9018b24f7ea9e3b9bad260f86359912707c5cb80aafa60b"
      }
    ]
  }
}
```

## CNAB
> **Note:** *this is just an illustrative example of the power of decoupling artifactTypes from mediaTypes, allowing reuse of existing artifacts*

> The persistance format has yet to be decided by the CNAB community, the following represents what a CNAB *could* be expressed as.


CNABs are interesting as they aren't a specific artifact in a registry, rather they may reference other artifacts in a registry. A CNAB can reference an invocation image, a Helm Chart, or several other images that may be deployed. It may also reference arm or cloud formation templates to establish infrastructure.

```json
"mediaType": "application/vnd.oci.manifest.v1+json",
"artifactType": "application/vnd.deislabs.cnab.manifest.v1"
```
**CNAB example, using Duffle**

`duffle install myblog demo42.azurecr.io/samples/cnab/wordpress:0.1.0`
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.manifest.v1+json",
  "artifactType": "application/vnd.deislabs.cnab.v1",
  "config": {
    "manifests": [
      {
        "mediaType": "application/vnd.oci.config.v1+json",
        "artifactType": "application/vnd.deislabs.cnab.config.v1",
        "size": 4288,
        "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
      }
    ]
  },
  "manifests": [
    {
      "mediaType": "application/vnd.oci.index.v1+json",
      "artifactType": "application/vnd.oci.image.index.v1",
      "size": 4288,
      "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
    },
    {
      "mediaType": "application/vnd.oci.index.v1+json",
      "artifactType": "application/vnd.oci.image.index.v1",
      "size": 93288,
      "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
    },
    {
      "mediaType": "application/vnd.oci.index.v1+json",
      "artifactType": "application/vnd.cncf.helm.v3",
      "size": 2828,
      "digest": "sha256:a50c80b8ad64bd98f3f50770714cbe2904951d32c5a59860f74eb7b89958eb5e"
    },
    {
      "mediaType": "application/vnd.oci.manifest.v1+json",
      "artifactType": "application/vnd.aws.ecs.task.v1",
      "size": 2828,
      "digest": "sha256:4a2f7cd2e53307d7e9018b24f7ea9e3b9bad260f86359912707c5cb80aafa60b"
    },
    {
      "mediaType": "application/vnd.oci.manifest.v1.tar+gzip",
      "artifactType": "application/vnd.deislabs.cnab.component.v1",
      "size": 7023,
      "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7"
    },
    {
      "mediaType": "application/vnd.oci.manifest.v1+json",
      "artifactType": "application/vnd.microsoft.arm.template.v1",
      "size": 7023,
      "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7"
    }
  ]
}
```
> See [/cnab/readme.md](./cnab/readme.md) for more details of the decoupled proposal.