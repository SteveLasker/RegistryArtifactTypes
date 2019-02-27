# OCI Image w/Docs

Docs become a new OCI Manifest, which can be referenced by an OCI Index

## Index
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.image.index.v1+json",
  "manifests": [
    {
      "mediaType": "application/vnd.oci.image.manifest.v1+json",
      "size": 4288,
      "digest": "sha256:82qga5d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d563432",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.oci.image.manifest.v1+json",
      "size": 1200,
      "digest": "sha256:aasd8215d624131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5611821"
    }
  ]
}
```
## Doc Manifest

```json
{
    "schemaVersion": 2,
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "config": {
        "mediaType": "application/vnd.oci.doc.config.v1+json",
        "size": 7023,
        "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7"
    },
    "layers": [
        {
        "mediaType": "application/vnd.oci.doc.layer.v1.tar",
        "size": 1223,
        "digest": "sha256:9a1a13172ed974323f7c35153e8b23b8fa1c85355b6b26cc3127e640e45ef0aa"
        }  
    ]
}
```
