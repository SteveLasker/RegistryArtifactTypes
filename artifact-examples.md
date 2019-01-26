# Image:Tags to manifests

To represent a flow, a list of images and their associated manifests are included in the below table. Rather than show full sha digests, shortcut digests are saved for easier navigation.

`helm upgrade demo42.azurecr.io/samples/hello-world:1.0`

| Artifact Type | namespace/image:tag | manifest |
|-|-|-|
| image | demo42.azurecr.io/samples/image/hello-world:1.0 | [sha256:helloworld10](./helm-manifest.json) |
| helm chart | demo42.azurecr.io/samples/helm/hello-world:1.0 | [sha256:helloworld10](./helm-manifest.json) |
| arm template | demo42.azurecr.io/samples/arm/hello-world:1.0 | [sha256:helloworld10](./helm-manifest.json) |
| cnab | demo42.azurecr.io/samples/cnab/hello-world:1.0 | [sha256:helloworld10](./helm-manifest.json) |
