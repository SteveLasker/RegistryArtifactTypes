# CNAB - Cloud Native Application Bundle

CNABs are interesting as they aren't just a specific artifact in a registry, rather they reference other artifacts in a registry. A CNAB can reference an invocation image, a Helm Chart, or several other images that would be deployed. It may also reference arm or cloud formation templates to establish the infrastructure.

The cnab index can contain pointers to other artifacts in the registry. 

## CNAB Index

`application/vnd.oci.cnab.index.v3+json`

Contains a collection of manifests, including a configuration manifest which defines the execution of bundle, and any other registry artifact. A bundle is a collection of known and unknown registry artifact types.

In the following example, `oci.cnab.config` references a layer that hosts the configuration of the CNAB. This is where the invocation image is identified, and whether the invocation is a docker image, or some other executable; such as VM or other CNAB drivers.

The image references, including the invocation image, may reference a collection of multi-arch images. For instance the invocation image may support Linux, Windows and ARM. Since the index maintains a pointer to existing `oci.image.index` manifests, no additional schema is required.

A CNAB may contain references to helm charts (`cncf.helm.chart.index`), deferring the details to the helm registry spec. 

## CNAB Components

To support arbitrary objects, which may be 100k to 100gb, `oci.cnab.component` is used to identify blobs that have no external use, beyond a CNAB. 

By separating known registry artifacts from arbitrary blobs, a CNAB can reference objects that are individually searchable, while hiding objects that are only interesting to CNABs.

```yaml
{
  "schemaVersion": 2,
  "config": {
    "manifests": [
      {
        "mediaType": "application/vnd.oci.cnab.config.v3+json",
        "size": 4288,
        "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
      },
      {
        "mediaType": "application/vnd.oci.cnab.component.v1+json",
        "size": 93288,
        "digest": "sha256:u716dad6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5271612"
      },
      {
        "mediaType": "application/vnd.oci.image.index.v1+json",
        "size": 93288,
        "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
      },
      {
        "mediaType": "application/vnd.oci.image.index.v1+json",
        "size": 2828,
        "digest": "sha256:a50c80b8ad64bd98f3f50770714cbe2904951d32c5a59860f74eb7b89958eb5e"
      },
      {
        "mediaType": "application/vnd.cncf.helm.chart.index.v1+json",
        "size": 7023,
        "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7"
      }
    ]
  }
}
```
