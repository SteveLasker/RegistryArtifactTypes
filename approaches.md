## Artifact Type Approaches

The [OCI Image-spec](https://github.com/opencontainers/image-spec/) provides great flexibility for storing image layers, including the ability to store multi-arch images in a single index. 

While it's possible to store anything in these layers, having deterministic understanding of the type enables great experiences.

### Copying mediaType for additional artifacts

While several discussions have considered copying the existing [oci mediaTypes](https://github.com/opencontainers/image-spec/blob/master/media-types.md) to represent different artifact types, this approach conflates the persistence format with the actual artifact type. 

- ` "mediaType": "application/vnd.cncf.helm.chart.v1+json" `
- ` "mediaType": "application/vnd.deislabs.cnab.chart.v1+json" `

Expanding the media types requires registries to understand these new media types and how to validate them.

### Using annotations for additional artifacts

Other options considered [annotations](https://github.com/opencontainers/image-spec/blob/master/annotations.md) to describe the new artifact. 

```json
"annotations": {
    "io.cnab.component_name": "component-2",
    "com.docker.app.format": "cnab",
    "io.cnab.keywords": "[\"keyword1\",\"keyword2\"]",
    "io.cnab.runtime_version": "v1.0.0-WD",
    "io.cnab.type": "io.docker.app",
```

Annotations are great for optional search results and details of an artifact. However, annotations are optional, making it a little too unstructured to provide deterministic definitions of the objects within a registry.

While annotations can be fit within the pre-release OCI image-spec, it's proposed this is the time to evolve the spec to decouple persistance formating from artifact declaration.

## Decoupling Persistance Format from Artifact Type

The [OCI image-spec](https://github.com/opencontainers/image-spec/) expresses mediaTypes to define the persistance of an **OCI Image**. In this proposal, mediaTypes are more generic, expressing the hierarchy of persistance, regardless of the specific type. A new required `artifactType` is added, providing deterministic definition of the artifact.

By decoupling persistance from the type, a registry may persist the artifact without needing to know the artifactType. As long as the artifact follows the validated structure of the renamed [OCI medaiTypes](https://github.com/opencontainers/image-spec/blob/master/media-types.md), the object can be persisted. 

### Persistance based mediaTypes

The renamed, OCI mediaTypes have `image` removed to represent persistance format, leaving artifact type to a new property.

| MediaType | Usage |
|-|-|
|`application/vnd.oci.index.v1+json` | OCI Index |
|`application/vnd.oci.manifest.v1+json` | OCI Artifact Manifest |
|`application/vnd.oci.layer.v1.*` | OCI Layer |

### Artifact Type

To decouple persistance from artifact identification, a new required `artifactType` is proposed.

- **`artifactType`** *string*
  This property is REQUIRED, identifying the object the manifest represents. 

The following are proposed artifactTypes
| ArtifactType | Display Name | Info | 
|-|-|-|
|`application/vnd.oci.image.index.v1` | OCI Multi-Arch Image | [Docker](https://www.docker.com/products/docker-desktop) *|
|`application/vnd.oci.image.manifest.v1` | OCI Image | [Docker](https://www.docker.com/products/docker-desktop) * |
|`application/vnd.cncf.helm.manifest.v3` | Helm | [Helm](https://helm.sh)
|`application/vnd.deislabs.cnab.manifest.v1` | CNAB | [Duffle](https://cnab.io), [Docker-application](https://www.docker.com/search/node?keys=docker-application)|
|`application/vnd.microsoft.arm.manifest.v1`| ARM | [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) |
|`application/vnd.aws.ecs.task.manifest.v1`| ECS Task | [aws Task](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)|

*\* most registry providers automatically convert `oci.image` manifests to the format requested by the client.*

> See: [mediaTypes & artifactTypes](https://github.com/SteveLasker/RegistryArtifactTypes/blob/master/mediaTypes.md) for more details
