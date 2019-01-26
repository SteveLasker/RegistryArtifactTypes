# Helm 
Helm charts represent a [collection of files used for a kubernetes deployment](https://github.com/demo42/helloworld-deploy/tree/master/helm/helloworld). While a single helm chart could be represented as a single manifest and compressed layer, there are some interesting possibilities of reuse and change tracking. 
- **resuse** - just as image layers are shared across images, being able to track different usages of wordpress charts can be useful. While reuse can also account for saving space in a registry, the total size of charts are closely equivalent to the manifests and metadata used to track them.
- **change tracking** - when charts are used for a teams custom app, as the charts are deployed, images are continually rebuilt. For each build, a new/unique `:tag` should be created to assure consistent deployments. (see [Image Tagging Best Practices](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)). Rather than embed the image reference directly in [the chart](https://github.com/demo42/helloworld-deploy/blob/master/helm/helloworld/Chart.yaml), it's likely extracted, and either passed in as a parameter, or part of an updated [values.yaml](https://github.com/demo42/helloworld-deploy/blob/master/helm/helloworld/values.yaml) file. 

  example:
  ```sh
  helm upgrade customapp demo42.azurecr.io/apps/customapp:2.0 \
    --reuse-values \
    --set frontend.image=demo42.azurecr.io/apps/frontend:aad4
  ```

While the Helm community has yet to decide what level of granularity to store charts, we'll elaborate the more complex example to compare with images and CNAB. 

## Helm Index
`application/vnd.cncf.helm.index.v3+json`

Unlike images, helm charts do not need platform specific references. The images the chart references may be multi-arch, however the chart maintainer does not author different charts per platform.

However, the index can be used to account for the different chart elements, allowing for the chart and values file to be referenced individually. 

There are two different options:
- [wordpress-index-annotations.json](./wordpress-index-annotations.json)
  
  In this example, a single `helm.manifest` type is used, with annotations describing the sub type. While less structured, it provides a bit more flexibility for the helm client to determine elements without having to maintain mediaTypes. 
  ```json
  {
    "mediaType": "application/vnd.cncf.helm.manifest.v3+json",
    "size": 4288,
    "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127",
    "annotations": {
      "vnd.cncf.helm.type": "chart"
    }
  },
  {
    "mediaType": "application/vnd.cncf.helm.manifest.v3+json",
    "size": 323,
    "digest": "sha256:4a2f7cd2e53307d7e9018b24f7ea9e3b9bad260f86359912707c5cb80aafa60b",
    "annotations": {
      "vnd.cncf.helm.type": "values"
    }
  }
  ```
- [wordpress-index-types](./wordpress-index-types.json)

  In this example, we use specific mediaTypes `helm.chart` and `helm.values` to identify the different layers. Note: the layers are not embedded, allowing other charts to reuse the individual layers.
  ```json
  {
    "mediaType": "application/vnd.cncf.helm.chart.v3+json",
    "size": 4288,
    "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
  },
  {
    "mediaType": "application/vnd.cncf.helm.values.v3+json",
    "size": 323,
    "digest": "sha256:4a2f7cd2e53307d7e9018b24f7ea9e3b9bad260f86359912707c5cb80aafa60b"
  }
  ```
## Helm Chart

`application/vnd.cncf.helm.chart.v3+tar` 

example: [wordpress-chart.json](./wordpress-chart.json)

The mediaType for a layer, specific to a chart. By maintaining these individually, helm charts can reference an existing chart or values file, just as images layers are shared.

## Helm Values

`application/vnd.cncf.helm.values.v3+json` 

example: [wordpress-value.json](./wordpress-value.json)

The mediaType for a layer, specific to a values file.