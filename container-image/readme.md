# OCI Image

The OCI Image spec conflates the persistance format with the image spec definition. 
With the decoupling of persistance from what the object represents, we can define multiple artifacts with the existing `mediaTypes`

The following are examples of the changes required to represent an OCI Image.

The examples in this folder represent

- [Wordpress multi-arch image index](./wordpress-index.json)
- [Wordpress linux image manifest](./wordpress-manifest-linux.json)
- [Wordpress amd64 image manifest](./wordpress-manifest-amd64.json)
- [Mysql multi-arch image index](./mysql-index.json)
- [Mysql amd64 image manifest](./mysql-manifest-amd64.json)

## Deploying WordPress

A user can choose to deploy wordpress and mysql with Helm or CNAB. In either of those cases, these deployment artifacts will reference these images. 

See [CNAB](../cnab/readme.md) and [Helm](../helm/readme.md) examples