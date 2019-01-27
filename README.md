# Registry Artifact Types
OCI compliant registries are becoming [Cloud Native Artifact Registries](https://stevelasker.blog/2019/01/25/cloud-native-artifact-stores-evolve-from-container-registries/)

While a registry could just store arbitrary objects, leaving it to the user to know what blob should be used by which tool, there are many benefits to knowing what each artifact is.

Some of the challenges include: 

- If registries support all sorts of artifacts (multiple dozens of types) how do tools and users reason over them? 
- How would duffle search know to only list CNABs, Helm search list charts, Docker only list actual docker images and ARM or Cloud Formation tooling only list their templates?
- How does a registry listing (like docker hub) list the various artifacts, showing them as the types they are?
- How do registry listings know what actions to put on an artifact? (Run an image, deploy a Chart, install a foo2)?
- How do vulnerability scanners know what they’re pulling, so they know how to scan them?
- How do different teams, who work on independent components of a swath of services, push their independent components to the registry?
- How do we allow tools to work independently? Meaning, duffle, Helm, Docker-application, docker, foo2, shouldn’t have to know about the other artifacts, just to push their specific artifact to a registry?
- How do we sign artifacts, individually or as a group?
- How easily is it for the next artifact type to be supported by OCI registries? Does a registry owner need to do anything more than possibly know what icon to associate with the artifact type? Can the registry and tools easily understand it’s a foo2, compared to an image or other known artifacts?
- How does this all look when we have dozens and dozens of types? Imagine not knowing the type for all the files on your local hard drive. How would you double click them to launch something? How would each application open file dialog know which files it can open?

## Powerful, Flexible, but Structured or Unstructured?

The [OCI Distribution Image Specification](https://github.com/opencontainers/image-spec/) has a very open standard. While extremely powerful and flexible, without structure, we wind up with a cloud based file system full of blobs that tools will have no ability to differentiate. 

This repo attempts to demonstrate several structured use cases of the OCI Indexes to scale from multi-layered, multi-architecture docker images, to relatively simple helm charts, to more complex CNAB examples that can encompass every type of artifact in a registry.

## Expanded Media Types
To identify registry artifacts the proposal makes use of an [expanded list of media types](./mediaTypes.md)
## Baseline Reference - Docker Images

[Container images](./container-image/readme.md) are the baseline. There's nothing newly proposed here, rather reference examples, used for comparison in Helm and CNAB.

**example:**

```sh
docker run demo42.azurecr.io/samples/image/hello-world:1.0
```

## Helm Charts

[Helm charts](./helm/readme.md) represent a collection of files used for a kubernetes deployment. While a single helm chart could be represented as a single manifest and compressed layer, there are some interesting possibilities of reuse and change tracking.

**example**

```sh
helm upgrade \
  hello-world \
  demo42.azurecr.io/sample/helm/hello-world:1.0 \
  --reuse-values \
  --set web.image=demo42.azurecr.io/samples/image/hello-world:1.0
```
  ## CNAB - Cloud Native Application Bundles

[CNAB](./cnab/readme.md) is interesting as they aren't just a specific artifact in a registry, rather they reference other artifacts in a registry. A CNAB can reference an invocation image, a Helm Chart, or several other images that would be deployed. It may also reference arm or cloud formation templates to establish the infrastructure.

**example**

```sh
duffle install \
  hello-world \
  demo42.azurecr.io/sample/cnab/hello-world:1.0
```

