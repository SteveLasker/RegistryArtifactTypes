# mediaTypes & artifactTypes

Artifact tools will need the ability to query their specific artifacts from a registry. In addition, registry vendors will want to understand the different artifact types they present to their users, providing additional information about the artifact. Including, the ability to launch the associated tool.


## Media Types

The current [OCI image-spec](https://github.com/opencontainers/image-spec/) expresses mediaTypes define the persistance of an **OCI Image**. In this proposal, mediaTypes are more generic, expressing the hierarchy of persistance, regardless of the specific type. By decoupling persistance from the type, a registry may persist the artifact without needing to know the artifactType. As long as the artifact follows the validated structure of MedaiTypes, it can be persisted. 

The renamed, more generic OCI mediaTypes have `image` removed:

| MediaType | Usage |
|-|-|
|`application/vnd.oci.index.v1+json` | OCI Index |
|`application/vnd.oci.manifest.v1+json` | OCI Artifact Manifest |
|`application/vnd.oci.layer.v1.tar+gzip` | OCI Layer |

