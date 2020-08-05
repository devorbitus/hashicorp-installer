# HashiCorp Installer

HashiCorp Installer is a script and accompanying Docker image that downloads,
verifies, and installs HashiCorp tools from the HashiCorp releases service.

Features:

- **GPG import and verification** - automatically imports HashiCorp's GPG key
  and verifies the download integrity and signature

- **Docker container** - includes a Docker contain which can be used as a
  standalone way to install or can be used as a multi-stage pre-build container.

## Usage

The general syntax is:

```text
$ install-hashicorp-tool NAME VERSIONS [OS=linux [ARCH=amd64]]
```

For example:

```text
$ install-hashicorp-tool terraform 0.11.7
```
or for multiple versions:
```text
$ install-hashicorp-tool terraform '0.11.7 0.11.10 0.11.11'
```

### Standalone Installer

The tool can be used as a standalone installer:

```text
$ docker run -v $(pwd):/software devorbitus/hashicorp-multi-installer terraform 0.11.7
```

### Multi-Stage Builder

The tool can also be used as part of a multi-stage Docker build:

```dockerfile
# Download and verify the integrity of the download first
FROM devorbitus/hashicorp-multi-installer AS installer
RUN /install-hashicorp-tool "terraform" "0.11.7 0.11.10"

# Now copy the binary over into a smaller base image
FROM alpine:latest
COPY --from=installer /software/0.11.7 /terraform-0.11.7
COPY --from=installer /software/0.11.10 /terraform-0.11.10
ENTRYPOINT ["/terraform-0.11.10"]
```

## FAQ

**Why don't you import HashiCorp's public key from a keyserver?**<br>
A few reasons. First, then you have to trust the key server. Second, some build
services don't allow outbound requests on non-80, non-443 ports. Since most
keyservers don't run on those ports, it creates unreliable builds.

## Credit

All credit goes back to Seth Vargo for the [original script](https://github.com/sethvargo/hashicorp-installer) and [image](https://hub.docker.com/r/sethvargo/hashicorp-installer)
