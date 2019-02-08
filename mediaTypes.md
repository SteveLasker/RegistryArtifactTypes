# manifest.config.mediaTypes 

Artifact tools will need the ability to query their specific artifacts from a registry. In addition, registry vendors will want to understand the different artifact types they present to their users, providing additional information about the artifact. Including, the ability to launch the associated tool.

To uniquely identify artifact types within a registry, the `manifest.config.mediaType` will support additional `mediaTypes`. 

## Media Types

In this proposal, new `mediaTypes` are supported by the [config property descriptor](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest-property-descriptions)

By supporting new `config.mediaTypes`, registry tools will understand the types stored. 
A simplified example of a registry listing might look like:

| artifact reference | icon | type | actions|
|-|-|-|-|
| `samples/image/hello-world:1.0` |![](./images/oci-container.png)| container image | `docker run ...` |
| `samples/helm/hello-world:1.0` |![](./images/helm.png)|  helm chart | `helm install ...` |
| `samples/cnab/hello-world:1.0` |![](./images/cnab.jpg)| CNAB | `duffle install ...` |
| `samples/arm/hello-world:1.0` |![](./images/arm.png)| arm | `az deployment create ...` |
| `samples/ecs-task/hello-world:1.0` |![](./images/ecs.png)| ecs-task | `aws ecs create-service ...` |

An example CLI based registry listing:
```sh
az acr repository show-tags --name demo42 --repository hello-world

Repository: hello-world
Tag         Type
------------------
1.0-image   image
1.0-helm    helm
1.0-cnab    cnab
```

## Parsing `manifest.config.mediaType`
The [Config Descriptor in the OCI Image spec](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest-property-descriptions) identifies the `mediaType` MUST support the `application/vnd.oci.image.config.v1+json` config. 

- **`config`** *[descriptor](descriptor.md)*

    This REQUIRED property references a configuration object for a container, by digest.
    Beyond the [descriptor requirements](descriptor.md#properties), the value has the following additional restrictions:

    - **`mediaType`** *string*

        This [descriptor property](descriptor.md#properties) has additional restrictions for `config`.
        Implementations MUST support at least the following media types:

        - [`application/vnd.oci.image.config.v1+json`](https://github.com/opencontainers/image-spec/blob/master/config.md)

        Manifests concerned with portability SHOULD use one of the above media types.

Registry owners MAY support additional `manifest.config.mediaType`s. 
When additional a new `mediaType` is encountered, the parsing of the config blob is open to the new `mediaType` to define. 

The options are:

- [`application/vnd.oci.image.config.v1+json`](https://github.com/opencontainers/image-spec/blob/master/config.md)
- None
- New schemas required by the new `mediaType`


## MediaType Mappings
To ease registry support of new artifacts, registry owners can import [medaiTypeMappings](./mediaTypeMappings.json), allowing new artifact authors the ability to define meta-data for their types.


```json
{
    "schemaVersion": 1,
    "mediaTypes": [
        {
            "mediaType": "application/vnd.oci.image.config.v1+json",
            "title": "OCI Image",
            "description": "OCI Image Format",
            "icon.svg": "https://github.com/opencontainers/artwork/blob/master/oci/icon/color/oci-icon-color.svg",
            "icon.png": "https://github.com/opencontainers/artwork/blob/master/oci/icon/color/oci-icon-color.png",
            "moreInfo": "https://www.opencontainers.org/",
            "tools": [
                {
                    "title": "OCI Tools",
                    "installer": "https://github.com/opencontainers/runtime-tools"
                }
            ],
            "schemaValidation": "https://github.com/opencontainers/image-spec/blob/master/schema/config-schema.json"
        },
        {
            "mediaType": "application/vnd.cncf.helm.config.v3+json",
            "title": "Helm Chart",
            "description": "The package manager for Kubernetes",
            "icon.svg": "https://github.com/helm/helm/blob/master/docs/logos/helm-blue-vector.svg",
            "icon.png": "https://github.com/helm/helm/blob/master/docs/logos/helm_logo_transparent.png",
            "moreInfo": "https://helm.sh/",
            "tools": [
                {
                    "title": "Helm",
                    "installer": "https://github.com/helm/helm#install"
                }
            ],
            "schemaValidation": "none"
        },
        {
            "mediaType": "application/vnd.deislabs.cnab.config.v1+json",
            "title": "CNAB",
            "description": "Facilitate the bundling installing and managing of container-native apps",
            "icon.svg": "https://github.com/deislabs/cnab.io/blob/master/themes/cnab/static/img/cnab-icon.svg",
            "icon.png": "https://github.com/deislabs/cnab.io/blob/master/themes/cnab/static/img/logo.png",
            "moreInfo": "https://cnab.io/",
            "tools": [
                {
                    "title": "duffle",
                    "installer": "https://github.com/deislabs/duffle#duffle-the-cnab-installer"
                },
                {
                    "title": "docker-application",
                    "installer": "https://www.docker.com/products/docker-desktop"
                }
            ],
            "schemaValidation": "https://github.com/deislabs/cnab-spec/tree/master/schema/oci-config.json"
        }
    ]
}
```
