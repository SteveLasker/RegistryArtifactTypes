# Helm 
Helm charts represent a [collection of files used for a kubernetes deployment](https://github.com/demo42/helloworld-deploy/tree/master/helm/helloworld). While a single helm chart could be represented as a single manifest and compressed layer, there are some interesting possibilities of reuse and change tracking. 

- **resuse** - just as image layers are shared across images, being able to track different usages of wordpress charts can be useful. While reuse can also account for saving space in a registry, the total size of charts are closely equivalent to the manifests and metadata used to track them.
- **change tracking** - when charts are used for a teams custom app, as the charts are deployed, images are continually rebuilt. For each build, a new/unique `:tag` should be created to assure consistent deployments. (see [Image Tagging Best Practices](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)). Rather than embed the image reference directly in [the chart](https://github.com/demo42/helloworld-deploy/blob/master/helm/helloworld/Chart.yaml), it's likely extracted, and either passed in as a parameter, or part of an updated [values.yaml](https://github.com/demo42/helloworld-deploy/blob/master/helm/helloworld/values.yaml) file. 

**example:**

  ```sh
  helm upgrade customapp demo42.azurecr.io/apps/customapp:2.0 \
    --reuse-values \
    --set frontend.image=demo42.azurecr.io/apps/frontend:aad4
  ```

> **Note:** *this is just an illustrative example of custom mediaTypes.*

> The persistance format has yet to be decided by the Helm community, the following represents what a Helm chart *could* be expressed as.


## Helm Manifest

Unlike images, helm charts do not need platform specific references. The images the chart references may be multi-arch, however the chart maintainer does not typically author different charts per platform.

However, the manifest can be used to account for the different chart elements, allowing for the chart and values file to be referenced individually. 



## Helm Chart
> **Note:** *this is just an illustrative example of mediaTypes.*

> The persistance format has yet to be decided by the Helm community, the following represents what a Helm chart *could* be expressed as.

Unlike OCI Image manifests, Helm charts use layers to represent different elements of a chart. The files that make up the chart are separated from the values file *(parameter values)*. By separating values, multiple deployments of the same chart, only differentiated by the values, may be tracked and evaluated by new Helm tools.

The following represent `mediaTypes` used to represent manifests and layers of a Helm chart. The registry need not know of the `artifactTypes` for persisting the object. Rather, these are used by tooling specific to Helm, including other tools that may process the Helm object (eg: compliance scanners)


| mediaType | usage |
|-|-|
|`application/vnd.cncf.helm.config.v3`|Helm Config|
|`application/vnd.cncf.helm.chart.layer.v3`|Helm Chart archive layer|
|`application/vnd.cncf.helm.values.layer.v3`|Helm Values layer |

## Helm Examples

`helm install demo42.azurecr.io/samples/helm/wordpress:0.1.0`


The following are examples of a Wordpress chart:

- [Wordpress chart manifest](./wordpress-manifest.json), 


