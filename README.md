# Demo DevSecOps 2024 - Bologna

This repository has been created to provide practical support for the presentation "A Journey to Distroless,"  
delivered by Michele Buccarello and Matteo Bisi during DevSecOps Day 2024 in Bologna.  
The purpose of this repository is to provide an educational foundation to start creating and working with distroless container technology.  
The content of this repository is not intended to be production-ready.  

## How build a base distroless image

There are several ways to create a distroless base image.  
In the following examples, we will demonstrate use cases with the tool `apko`.  


### APKO

`apko` is the build tool that we will use in this PoC.

There are several ways to install and use `apko` (e.g., `brew install apko`).  
In this PoC, to ensure reproducibility across platforms, we will use the official container image:  

```bash
docker pull cgr.dev/chainguard/apko
```

To verify the `apko` version:

```bash
docker run --rm cgr.dev/chainguard/apko version
```

Now, move into the first folder:
```bash
cd 01_build_base_image
```
We are now ready to check the YAML file to understand how `apko` works, and then let's build our first base image:

```bash
docker run --rm -v ${PWD}:/work -w /work cgr.dev/chainguard/apko build wolfibase.yaml wolfi-base:test wolfibase.tar
```

Check the result!  (`ls -lhs`)

Now, we can repeat the process for the second YAML file: 
```bash
docker run --rm -v ${PWD}:/work -w /work cgr.dev/chainguard/apko build alpinebase.yaml alpine-base:test alpinebase.tar
```

We can now load the result directly into our local Docker registry: 
```bash
docker load < wolfibase.tar
docker load < alpinebase.tar
```
Have you noticed that JSON files have appeared in the folder? This files contains the SBOM in SPDX format. 

As last step we could make a compare with one of the common base images  
on the market: 
```bash
docker pull redhat/ubi8
```

compare the image size and the vulnerability detected between the images.

to scan the image locally with trivy 
```bash
trivy image alpine-base:test-amd64
trivy image wolfi-base:test-amd64
trivy image redhat/ubi8
```
## Build&Run a new image for a java application

Move to `02_build_java_image` and review `wolfieopenjdk21.yaml`.

Build the new image:  

```bash
docker run --rm -v ${PWD}:/work -w /work cgr.dev/chainguard/apko build wolfieopenjdk21.yaml wolfiopenjdk21-base:test wolfieopenjdk21.tar
```

Import it into the local Docker registry:

```bash
docker load < wolfieopenjdk21.tar
```

## Build & Run the Image

Now, let's move inside the folder `03_run_java_image`:
```bash
docker build -t my-java-app01:wolfie .
docker run -it --rm -p 8080:8080 my-java-app01:wolfie
```

Now we can try to build the same Java app using the official Maven image and compare the differences  
in terms of size and vulnerabilities:

```bash
docker build -f Dockerfile-MavenOfficial -t my-java-app01:maven .
docker run -it --rm -p 8082:8080 my-java-app01:maven
```

Using trivy (eg `brew install trivy`)
```bash
trivy image my-java-app01:maven
trivy image my-java-app01:wolfie
```

## Manual distroless
OK, now that we have understood how to build the images with APKO and see that everything is working as expected, let's try a different approach.  
Move into `04_Manual_distroless`:

```bash
docker build -t my-manual-distroless:1.0 .
```

It works!
But what are the cons?

- No SBOM generated
- Manual process
- Limited compatibility with vulnerability scanners
- Manual process, prone to errors

## USEFUL RESOURCES

| Resource  | Link |
|----------|----------|
| apko Overview, Chainguard Academy | https://edu.chainguard.dev/open-source/build-tools/apko/overview/ |
| apko github repo  | https://github.com/chainguard-dev/apko |
| #apko channel on Kubernetes Slack | https://slack.kubernetes.io/ | 
| melange Overview, Chainguard Academy | https://edu.chainguard.dev/open-source/build-tools/melange/overview/ |