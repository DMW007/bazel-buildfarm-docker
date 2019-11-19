# Bazel-Buildfarm on Docker

Run [bazel-buildfarm](https://github.com/bazelbuild/bazel-buildfarm) master and worker nodes on Docker (Compose). Supports MITM-proxys for corporate usage.

## Build arguments

| Name                     | Default                                                    | Description                                                                                                                     |
| ------------------------ | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `BAZEL_VERSION`          | 1.1.0                                                      | Version of bazel to use                                                                                                         |
| `PROXY`                  | (Empty)                                                    | Set it to `http://<user>:<password>@<proxyserver>:<port>` if proxy usage is required for internet access.                       |
| `JAVA_KEYSTORE`          | /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/security/cacerts | Keystore used for Bazel - All certificates in `proxy-certs/*.crt` are added to this store. You usually don't need to change it. |
| `JAVA_KEYSTORE_PASSWORD` | changeit                                                   | Password of `JAVA_KEYSTORE`                                                                                                     |

### Use build arguments

In `docker-compose`, you pass them like this to the corresponding container:

```yaml
services:
  server:
    build:
      context: .
      args:
        PROXY: "http://<user>:<password>@<proxyserver>:<port>"
```

## Configuration

## Behind a proxy

If you're behind a (corporate) proxy, specifiy the build argument `PROXY` as described above. When the proxy breaks encrypted traffic using MITM, the root certificiate is also required. Save the root certificate with `.crt` extension in the `proxy-certs` folder. The name doesn't matter. All files ending with `.crt` are added to the key store of the OS as well as Java's store.

**Important** Please make sure that the folder `proxy-certs` _exists_ and contains _at least one file_. The default `README` from our repo is enough. This is also required when you don't want to use a proxy since docker can't conditionally copy files. For this reason we always copy the content if `proxy-certs` to support proxy users, too.
