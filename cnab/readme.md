# CNAB - Cloud Native Application Bundle

> **Note:** *this is just an illustrative example of the power of decoupling artifactTypes from mediaTypes, allowing reuse of existing artifacts*

> The persistance format has yet to be decided by the CNAB community, the following represents what a CNAB *could* be expressed as.


CNABs are interesting as they aren't a specific artifact in a registry, rather they may reference other artifacts in a registry. A CNAB can reference an invocation image, a Helm Chart, or several other images that may be deployed. It may also reference arm or cloud formation templates to establish infrastructure.

CNABs adds `mediaTypes` representing the `cnab.config`. This config layer is used to identify the invocation image and `cnab.component` which represents objects that are otherwise unknown to registries. A `cnab.config` may indicate whether the bundle uses an oci-image as it's invocation image, or another artifact that may be represented as a `cnab.component`. To support configurations unique to CNAB, CNAB implements it's own config schema, used by it's tooling.

In the example below, the existing `application/vnd.oci.image.manifest.v1+json` type is used to represent invocation images. While `application/vnd.deislabs.cnab.component.v1` is used to represent new opaqe types, unique to CNAB. 


| mediaType | usage |
|-|-|
|`application/vnd.deislabs.cnab.config.v1`|CNAB Config|
|`application/vnd.deislabs.cnab.component.v1`|CNAB component|


## CNAB Components

To support arbitrary objects, which may be 100k to 100gb, `deislabs.cnab.component` is used to identify blobs that have no external use, beyond a CNAB. 

By separating known registry artifacts from arbitrary blobs, a CNAB can reference objects that are individually searchable, while hiding objects that are only interesting to CNABs.

## CNAB Examples
**CNAB example, using Duffle**

In the following example, `deislabs.cnab.config` references a layer that hosts the configuration of the CNAB. This is where the invocation image is identified, and whether the invocation is a docker image, or some other executable; such as VM or other CNAB drivers.

The image references, including the invocation image, may reference a collection of multi-arch images. For instance the invocation image may support Linux, Windows and ARM. Since the index maintains a pointer to an existing `oci.image.index` manifests, no additional schema is required.

`duffle install myblog demo42.azurecr.io/samples/cnab/wordpress:0.1.0`

The following are examples of of a Wordpress bundle:

- [Wordpress CNAB](./wordpress-cnab-manifest.json), which references other artifacts within the registry

