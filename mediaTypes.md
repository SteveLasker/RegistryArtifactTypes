# Expanded List of OCI Media Types

In addition to specfic artifact tools, each registry vendor will want to understand the different artifact types to present them to their users, providing additional information about the artifact. Including, the ability to launch the associated tool.

## image

existing OCI  image media types, listed for reference from [OCI Media Types](https://github.com/opencontainers/image-spec/blob/master/media-types.md)

- `application/vnd.oci.image.index.v1+json` [Image Index](https://github.com/opencontainers/image-spec/blob/master/image-index.md) - A higher-level manifest which points to platform/architecture specific image manifests
- `application/vnd.oci.image.manifest.v1+json` [Image Manifest](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest) providing configuration and set of layers for a single container image for a specific architecture and operating system 

## helm chart
- `application/vnd.cncf.helm.index.v3+json` [Helm Index](./helm/readme.md) of manifest types, making up a chart. 
- `application/vnd.cncf.helm.chart.v3+tar` [Helm Chart](./helm/readme.md#Helm-Chart) and all it's sub files
- `application/vnd.cncf.helm.values.v3+json` [Helm Values](./helm/readme.md#Helm-Values) extracted for traceability


## Azure Resource Manager (ARM)
Azure resource manager template. Used as reference to show the breadth of artifacts that will likely move into registries.

- `application/vnd.microsoft.arm.index.v1+json` **ARM Index** listing the layers representing a chart and it's parameters file
- `application/vnd.microsoft.arm.template..v3+tar` **Arm manifest** 
- `application/vnd.microsoft.arm.parameters.v1+json` **Arm Parameter Manifest** 

## AWS Cloud Formation Templates 


- `application/vnd.aws.cft.template.v1+json` **Cloud formation manifest** 
- `application/vnd.aws.cft.parameters.v1+json` **Cloud formation Parameter manifest**

## CNAB
Cloud Native Application Bundle
Bundles have unique media types, but also reference existing media types in a registry, such as all of the above types.

- `application/vnd.oci.cnab.index.v1+json` [CNAB Index](./cnab/readme.md) 
- `application/vnd.oci.cnab.config` [CNAB Config](./cnab/readme.md#config) identifying invocation entry point/image
- `application/vnd.oci.cnab.component.v1+json` [CNAB Component](./cnab/readme.md#component), an arbitrary object, not otherwise referenced as a OCI media type, contained within the bundle |

## Media Type Short Names

To provide for user friendly names, a list of media types and short/friendly names will be maintained. A link to the associated tool or info about the artifact will also be provided. 

| Media Type | Display Name | Info | 
|-|-|-|
|`application/vnd.oci.image.index.v1+json` | Image | [Docker](https://www.docker.com/products/docker-desktop) |
|`application/vnd.oci.image.manifest.v1+json` | Image | [Docker](https://www.docker.com/products/docker-desktop) |
|`application/vnd.cncf.helm.index.v3+json` | Helm | [Helm](https://helm.sh)
|`application/vnd.oci.cnab.index.v1+json` | CNAB | [Duffle](https://cnab.io)


