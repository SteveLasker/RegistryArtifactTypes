# mediaTypes 

Artifact tools will need the ability to query their specific artifacts from a registry. In addition, registry vendors will want to understand the different artifact types they present to their users, providing additional information about the artifact. Including, the ability to launch the associated tool.



## Media Types

The current [OCI image-spec](https://github.com/opencontainers/image-spec/) expresses mediaTypes define the persistance of an [OCI Image-manifest](https://github.com/opencontainers/image-spec/blob/master/manifest.md) and a collection of [OCI Images, hashed by platform-index](https://github.com/opencontainers/image-spec/blob/master/image-index.md). 



In this proposal, additional `mediaTypes` are added 
`mediaTypes` are more generic, expressing the hierarchy of persistance, regardless of the specific type. By decoupling persistance from the type, a registry may persist the artifact without needing to know the artifactType. As long as the artifact follows the validated structure of MedaiTypes, it can be persisted. 

The renamed, more generic OCI mediaTypes have `image` removed:

| MediaType | Usage |
|-|-|
|`application/vnd.oci.index.v1+json` | OCI Index |
|`application/vnd.oci.manifest.v1+json` | OCI Artifact Manifest |
|`application/vnd.oci.layer.v1.tar+gzip` | OCI Layer |

## Artifact Types
[artifactTypes](./artifactTypes.md) express what the `mediaType` represents.
