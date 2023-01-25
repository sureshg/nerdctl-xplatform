# Container/Kubernetes Playground!

[![GitHub Workflow Status][gha_badge]][gha_url]
[![Docker Linter][lint_img]][lint_url]

[![OpenJDK App][openjdk_app_img]][container-images]
[![Native Image App][nativeimage_app_img]][container-images]

Container/K8S/Compose playground using [dockerd(moby)][7]/[nerdctl][2]/[Rancher Desktop][3].

### Build & Run

```bash
# Build OpenJDK jLinked image with App CDS
$ DOCKER_BUILDKIT=1 docker build -t sureshg/app:latest --target openjdk .
$ docker run -it --rm -p 8080:80 sureshg/app:latest

# Build GraalVM native static image
$ DOCKER_BUILDKIT=1 docker build -t sureshg/graalvm-static --target graalvm-static .
$ docker run -it --rm -p 8080:80 sureshg/graalvm-static
$ curl http://localhost:8080

# OpenJDK HSDIS image to print assembly
$ DOCKER_BUILDKIT=1 docker build -t sureshg/openjdk-hsdis:latest --target openjdk-hsdis .
$ docker run \
        -it \
        --rm \
        --volume "$(PWD)":/app \
        --workdir /app \
        --publish 8080:8080 \
        sureshg/openjdk-hsdis:latest src/App.java
        
# Debug distroless images
$ brew install cdebug         
$ cdebug exec \
         --image nixery.dev/busybox/curl \
         --privileged \
         -it \
         --rm \
         --platform linux/amd64 \
         docker://openjdk-playground               
```

### Misc

- Install [Rancher Desktop][3] with [containerd][0] [multi-platform][1] support

  ```bash
  # Install Rancher Desktop and Select containerd as runtime.
  $ sudo docker run --privileged --rm tonistiigi/binfmt --install all
  $ rdctl shell
     $ ls -1 /proc/sys/fs/binfmt_misc/qemu*
       /proc/sys/fs/binfmt_misc/qemu-aarch64
       /proc/sys/fs/binfmt_misc/qemu-arm
       /proc/sys/fs/binfmt_misc/qemu-mips64
       /proc/sys/fs/binfmt_misc/qemu-mips64el
       /proc/sys/fs/binfmt_misc/qemu-ppc64le
       /proc/sys/fs/binfmt_misc/qemu-riscv64
       /proc/sys/fs/binfmt_misc/qemu-s390x

  $ docker run --rm --platform=linux/arm64 alpine uname -a
  $ docker run --rm --platform=linux/s390x alpine uname -a
  
  
  # Check for all the OS archs
  $ for i in `docker images --format {{.ID}}`; do echo $i `docker image inspect $i | grep -e Architecture -e Os`; done
  
  # Remove unused data
  $ docker system prune -f
  # OR 
  # docker system df && docker image prune -f && docker container prune -f && docker network prune -f && docker volume prune -f
  ```

## Resources

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [A collection of docker-compose files][6]
- [Runtime privilege and Linux capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)
- [Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/)

[0]: https://github.com/containerd/containerd

[1]: https://github.com/containerd/nerdctl/blob/master/docs/multi-platform.md

[2]: https://github.com/containerd/nerdctl

[3]: https://github.com/rancher-sandbox/rancher-desktop

[4]: https://k3s.io/

[5]: https://github.com/jpetazzo/minimage

[6]: https://github.com/jonatan-ivanov/local-services

[7]: https://github.com/moby/moby


[gha_url]: https://github.com/sureshg/containers/actions/workflows/container-build.yml

[gha_img]: https://github.com/sureshg/containers/actions/workflows/container-build.yml/badge.svg

[gha_badge]: https://img.shields.io/github/actions/workflow/status/sureshg/containers/container-build.yml?branch=main&color=green&label=Container%20Build&logo=Github-Actions&logoColor=green&style=for-the-badge

[lint_url]: https://hadolint.github.io/hadolint/

[lint_img]: https://img.shields.io/badge/Dockerfile%20Linter-%E2%9D%A4-2596ec.svg?logo=Docker&style=for-the-badge&logoColor=2596ec

[openjdk_app_img]: https://ghcr-badge.deta.dev/sureshg/containers/size?tag=openjdk-latest&label=OpenJDK%20App

[nativeimage_app_img]: https://ghcr-badge.deta.dev/sureshg/containers/size?tag=nativeimage-latest&label=NativeImage%20App

[container-images]: https://github.com/sureshg/containers/pkgs/container/containers