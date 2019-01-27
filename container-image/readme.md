# Container Image Manifest Formats

These are just provided as reference, and used as references from CNAB and Helm manifests.

## WordPress
`docker run wordpress` requests the [index](./wordpress-index.json). If the host is linux, the docker daemon will pull the [linux manifest](./wordpress-manifest-linux.json), which lists the image layers required to represent the complete wordpress image. As the docker daemon processes the manifest list, it compares it's local cache to only pull the layers it doesn't have.

## MySql

Likewise, `docker run mysql` requests the [index](./mysql-index.json). The docker daemon pulls the [linux manifest](./mysql-manifest-amd64.json), and proceeds with the same local cache comparison, pulling layers it does not have..

## Deploying WordPress

A user can choose to deploy wordpress and mysql with Helm or CNAB. In either of those cases, these deployment artifacts will reference these images. 

See [CNAB](../cnab/readme.md) and [Helm](../helm/readme.md) examples