# Demo DevSecOps 2024 - Bologna

This repository has been built to provide some practical hints in support of the presentation 'A Journey to Distroless,' 
delivered by Michele Buccarello and Matteo Bisi during DevSecOps Day 2024 in Bologna. 

## How build a base distroless image

There are many different ways to create a distroless base image. 
In the following examples, we will explain some use cases using the tools apko and melange. 

### APKO

apko is the tool that we will use into this poc

There are several ways to install and use apko, 
in this poc we will use it from the offial container image

```
docker pull cgr.dev/chainguard/apko
```

to verify the apko version
```
docker run --rm cgr.dev/chainguard/apko version
```

now move into the first folder 
```
mv 01_build_base_image
```

Let's build our first base image
```
docker run --rm -v ${PWD}:/work -w /work cgr.dev/chainguard/apko build wolfibase.yaml wolfi-base:test wolfibase.tar
```
check the result !!

Now go ahed with the second baseimage
```
docker run --rm -v ${PWD}:/work -w /work cgr.dev/chainguard/apko build alpinebase.yaml alpine-base:test alpinebase.tar
```

We can now load both images into docker 
```
docker load <  wolfibase.tar
docker load <  alpinebase.tar
```



docker pull redhat/ubi8

###Let's see how this could work to build&run an application

Now move to 02_build_java_image and read wolfieopenjdk21.yaml

Build the new image

```
docker run --rm -v ${PWD}:/work -w /work cgr.dev/chainguard/apko build wolfieopenjdk21.yaml wolfiopenjdk21-base:test wolfieopenjdk21.tar
```
and import it into the local docker registry

docker load <  wolfieopenjdk21.tar

##BUILD&RUN the image


docker build -t my-java-app01:wolfie .
docker run -it --rm -p 8080:8080 my-java-app01:wolfie

Now we could try to build the same java app using the official maven image and check the difference in terms of size and vulnerabilities

docker build -f DockerFile-official -t my-java-app01:maven


docker run -it --rm -p 8082:8080 myapp01-eclipseofficial:1.0

##USEFUL RESOURCES

apko Overview Chainguard Academy https://edu.chainguard.dev/open-source/build-tools/apko/overview/
apko github repo:https://github.com/chainguard-dev/apko