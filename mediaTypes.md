# A list of media types

|displayType|mediaType|description|
|-|-|-|
| **image** | | docker image|
| image index |[application/vnd.oci.image.index.v1+json](https://github.com/opencontainers/image-spec/blob/master/image-index.md) | A higher-level manifest which points to specific image manifests, ideal for one or more platforms |
| image manifest |[application/vnd.oci.image.manifest.v1+json](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest) |provides a configuration and set of layers for a single container image for a specific architecture and operating system |
| **helm** | | helm chart|
| helm manifest | application/vnd.cncf.helm.index.v3+json | Root of a Helm Chart, incorporating the chart and values |
| helm chart | application/vnd.cncf.helm.chart.v3+tar | The helm chart, and all it's sub files, in tar format |
| helm values | application/vnd.cncf.helm.values.v3+json | helm values file, in json format. By storing in json format, it can be quickly and easily displayed in html tooling |
| **arm** | | azure resource manager template |
| arm manifest | application/vnd.cncf.arm.index.v1+json | An Azure Resource Manager template | 
| arm template | application/vnd.microsoft.arm.template..v3+tar | The arm template |
| arm paramters | application/vnd.microsoft.arm.parameters.v1+json | Parameters file for the arm template |
| **cloud formation** | | aws cloud formation template |
| cloud formation manifest | application/vnd.aws.cft.template.v1+json | The aws cloud formation template |
| cloud formation ___ | pplication/vnd.aws.cft.__.v1+tar| some additional cloud formation type|
| **cnab** | | Cloud Native Applicaiton Bundle |
| cnab| application/vnd.oci.cnab.v1+json |
| cnab config application/vnd.oci.cnab.config.v1+json | cnab configuration information, including the invocation entrypoint |
| cnab component | application/vnd.oci.cnab.component.v1+json | an arbitrary object, not otherwise referenced as a OCI media type, contained within the bundle |


