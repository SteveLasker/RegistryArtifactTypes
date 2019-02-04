`application/vnd.oci.manifest.v1+json` MUST contain at least one manifest:
```json
"mediaType": "application/vnd.oci.manifest.v1+json",
"artifactType": "application/vnd.oci.image.manifest.v1+json"
```
**Note:** See: [OCI Image-spec/Image-Index](https://github.com/opencontainers/image-spec/blob/master/image-index.md#image-index-property-descriptions) for architecture requirements:


**OCI Image Index example**
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
## OCI Image Manifest
Unlike the [image index](#OCI-Image-Index), which contains information about a set of images that can span a variety of architectures and operating systems, an image manifest provides a configuration and set of layers for a single container image for a specific architecture and operating system.

```json
"mediaType": "application/vnd.oci.manifest.v1+json",
"artifactType": "application/vnd.oci.image.manifest.v1+json"
```
OCI Image manifest contains a collection of layers, that represent a merged file system. The layers are separated to enable layer caching, reducing the time incremental changes are pulled to each node.

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





--------------------














# Container Image Manifest Formats

These are just provided as reference, and used as references from CNAB and Helm manifests.

## WordPress
`docker run wordpress` requests the [index](./wordpress-index.json). If the host is linux, the docker daemon will pull the [linux manifest](./wordpress-manifest-linux.json), which lists the image layers required to represent the complete wordpress image. As the docker daemon processes the manifest list, it compares it's local cache to only pull the layers it doesn't have.

## MySql

Likewise, `docker run mysql` requests the [index](./mysql-index.json). The docker daemon pulls the [linux manifest](./mysql-manifest-amd64.json), and proceeds with the same local cache comparison, pulling layers it does not have..

## Deploying WordPress

A user can choose to deploy wordpress and mysql with Helm or CNAB. In either of those cases, these deployment artifacts will reference these images. 

See [CNAB](../cnab/readme.md) and [Helm](../helm/readme.md) examples