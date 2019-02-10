# Registries To Become Cloud Native Artifact Stores
One of the many benefits Docker innovated in the VM to container paradigm shift was the integration of a registry. While VMs provided a means to run various virtual environments, there was no default solution for where VM images would be stored. With Docker, customers simply execute `docker run demo42.azurecr.io/marketing/webapp:1`. This single command facilitates a host to authenticate, find, pull and run a virtual environment. Where the image is sourced from is simplified to a registry URL. As a result, registries are core to the development and production environments.
All major cloud vendors have implemented [docker/distribution](https://github.com/docker/distribution) to support the registry api, implementing their cloud specific storage and security semantics. With the volume of development and production activity, these registries are hardened, secured and highly scalable and available. 

## New Cloud Native Artifact Types
As the industries looks to build atop image artifacts, new artifact types are surfacing.
- **Helm Charts are a common means to define a deployment in Kubernetes.** 
Several efforts have been made to support storing Helm Charts, such as Chart Museum. JFrog, [Codefresh](https://codefresh.io/docs/docs/new-helm/managed-helm-repository/) and Harbor implemented Chart Museum with reasonable success. [Quay](https://coreos.com/blog/quay-application-registry-for-kubernetes.html), and [Azure Container Registry](https://aka.ms/acr/helm-repos) leveraged the registry infrastructure to provide Helm Charts.
- **[CNAB](https://cnab.io/) – Cloud Native Application Bundles** were introduced in December '18, facilitating the bundling, installing and managing of container-native apps — and their coupled services.

Some existing and new artifact types are also looking for a place to be stored, with the assumption being to leverage a container registry:
- [Terraform requested to as a supported artifact type in Harbor](https://github.com/goharbor/harbor/issues/6598)
- Azure Resource Manager (ARM) and AWS Cloud Formation templates could also benefit from a place to be stored and referenced for deployment

## Leveraging Registry Investments
Implementing new artifact services are expensive investments. As [containers are becoming the common unit of deployment](https://stevelasker.blog/2016/05/26/docker-containers-as-the-new-binaries-of-deployment/), it's logical to assume development and production environments are already being configured to utilize, secure and manage registries. While we're not suggesting existing NPM, NuGet or other package managers should switch, we believe leveraging existing registries can enable new package artifacts to innovate quickly. 

## Representing Various Artifact Types
If we assume registries can host various artifact types, which the [OCI Distribution spec](https://github.com/opencontainers/distribution-spec) already allows for, the next set of question may involve:
- How does one reason over the various artifact types in a registry?
- How would HTML and API registry listings represent the different artifact types?
- If HTML registry listings wish to provide optimized action points, such as gestures to deploy a CNAB or Helm Chart, or deploying an image directly to a container service, how would the UI know which actions to surface on which artifact types?

While a registry can simply provide generic storage, one could ask, what value does it provide above basic storage? 

The answer is multi-fold:
- Registries abstract the storage and most of the cloud specfic security implementations. Whether the registry is baced by AWS S3  or Azure Storage is a detail the user, and the client CLI doesn't have to deal with
- Registries handle multi-part artifacts, with manifests providing pointers to reused artifacts. 

## CLIs Working Across Different Registries
The `docker` CLI is a great example of being able to use the same CLI across different cloud vendors. Any [docker/distribution](https://github.com/docker/distribution) based registry can support `docker pull & push`, among other common registry experiences. 

To carry this experience further, there are proposals for enabling [HELM](https://github.com/helm/community/pull/55) and CNAB CLIs to have equal universal experiences across different OCI compliant registries. 

If we agree a registry must be able to reason across it's different artifact types, and clients must be able to push specific artifacts into different OCI compliant registries, we would need a set of well known artifact types.

## Well Known Artifact Types
One could argue that package managers are specific as they have tailored experiences around them. Most registries provide experiences over the various artifacts they represent, however today this is limited to container images. If a registry is to host multiple artifact types, with the ability to reason over them uniquely, they will need to be well known.

Today, there are a relatively low number of [docker/distribution](https://github.com/docker/distribution) implementations. As containers continue to expand their usage, that number will successfully grow to unwieldy numbers. 

At the same time, new artifact types will surface. If registries whish to provide tailored experiences, and different tooling solutions will need to understand how to interact with the different artifact types, consistently across different registries, one could suggest we need a clearing house for artifact type definitions.
















## Common Referencing
As registries expand the capability of the types of artifacts they can support, the question of how they can be referenced comes into question. 

## Nested Namespaces & Folders
Regisries support namespaces for structruing storage of a vast rang of images. 
Namespaces aren't limited to one or two depths, but infinite depths, limited only by the lenght of a URI.
The following namespaces are valid:
- mcr.microsoft.com/windows/servercore:1803
- mcr.microsoft.com/mssql/server:2019-CTP2.0-ubuntu
- demo42.azurecr.io/marketing/campaigns/shoes/superbowl:1
- demo42.azurecr.io/marketing/campaigns/shoes/profile-api:1
- demo42.azurecr.io/marketing/campaigns/shoes/emailer:1

With the additional support of new artifacts, such as Helm and CNAB, the question becomes whether logically grouped artifacts can be stored together. 
Assuming we had a chart, to deploy the superbowl, profile-api and emailer, the chart may be saved to:
- demo42.azurecr.io/marketing/campaigns/shoes/superbowl:1
This can be a collision of the web front end, named superbowl, and the chart, also named superbowl

The difference between the two are the artifact types. 

Using a folder comparison, a user can store different file types in the same folder. On Windows, the file types are differentiated by it's extension `(.js, .cs, .jpg, .yaml)`. On Mac, the file types are metadata values. 

While we could limit different artifact types to different names, this does provide a constraint that would likely be a usability problem.

## Two Teams Sharing the Same Repo w/Different Tools
In the above scenario, two sub-teams may work on the same "application". One group focuses on the UI and backend services (images). While another team is focused on the deployment configuration (Helm Chart)

The development team uses `docker` to build and push images to demo42.azurecr.io/marketing/campaigns/shoes/, while the deployment team also pushes to demo42.azurecr.io/marketing/campaigns/shoes/ with the `helm` 3 client. 

When the 


## URIs and MimeTypes