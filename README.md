# Demo DevSecOps 2024 - Bologna

This repository has been built to provide some practical hints in support of the presentation 'A Journey to Distroless,' 
delivered by Michele Buccarello and Matteo Bisi during DevSecOps Day 2024 in Bologna. 

## How build a base distroless image

There are many different ways to create a distroless base image. 
In the following examples, we will explain some use cases using the tools apko and melange. 

### APKO

apko is the tool that we will use into this poc

the easiest way to install apko on your computer is to pull it as container

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

```
docker run --rm -v ${PWD}:/work -w /work cgr.dev/chainguard/apko build wolfieopenjdk21.yaml wolfiopenjdk21-base:test wolfieopenjdk21.tar
```

docker load <  wolfieopenjdk21.tar

ripartire da qui https://github.com/sighupio/ssc/tree/main/examples/eclipse-temurin
docker wolfiopenjdk21-base:test-amd64