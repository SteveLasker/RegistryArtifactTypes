# Container Image Manifest Formats

These are just provided as reference, and used as references from CNAB and Helm manifests.

## WordPress
`docker run wordpress` requests the [index](./wordpress-index.json). If the host is linux, the docker daemon will pull the [linux manifest](./wordpress-manifest-linux.json).

## MySql

Likewise, `docker run mysql` requests the [index](./mysql-index.json). The docker daemon may pull the [linux manifest](./mysql-manifest-amd64.json).

## Deploying WordPress

A user can choose to deploy wordpress and mysql with Helm or CNAB. In either of those cases, these deployment artifacts will reference these images. 

See [CNAB](../cnab/readme.md) and [Helm](../helm/readme.md) examples