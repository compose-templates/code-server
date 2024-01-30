# Code Server Web IDE

This project is a "local cloud development environment".

## How to start

> - git clone this repository
> - The parameters (architecture and version of the installed softwares) are defined in the `.env`. (choose between `arm64.env` and `amd64.env`)

**Start the IDE**:
```bash
docker compose --env-file arm64.env up  -d
# docker compose --env-file amd64.env up  -d
```
> - ✋ if you are on a arm architecture (mac silicon or linux arm) use `arm64.env`
> - otherwise use `amd64.env`

Then go to: http://0.0.0.0:4060/?folder=/workspace

If you updated `./.docker/compose/Dockerfile` or get a new version of this project, run this command before starting the builder:
```bash
docker compose --env-file arm64.env build
# docker compose --env-file amd64.env build
```

**Stop the builder**:
```bash
docker compose --env-file arm64.env down
# docker compose --env-file amd64.env down
```

## Add "tasks" at start of the Web IDE

If you want to start some initial tasks (like installing VSCode extension) once the Simplism IDE container is started, you can update `./.tasks/init.sh`

## How to

### Add a TLS certificate

Update the `*.env` files with the name of the certificate and the key:

For example:
```
LOCAL_DOMAIN=ide.simplism.cloud
TLS_CERT=ide.simplism.cloud.crt
TLS_CERT_KEY=ide.simplism.cloud.key
```

- Copy the certificate and the key to the `./certs` folder
- Give the appropriates rights to the files: `chmod 777 ide.simplism.cloud.*`
- Add this to your hosts file: `0.0.0.0 ide.simplism.cloud`
- Use this entrypoint in the `compose.yaml` file: `entrypoint: ["code-server", "--cert", "/workspace/certs/${TLS_CERT}", "--cert-key", "/workspace/certs/${TLS_CERT_KEY}", "--auth", "none", "--host", "0.0.0.0", "--port", "${CODER_HTTP_PORT}", "/workspace"]`

open https://ide.simplism.cloud:3000

### Use Mkcert to generate your own certificates

Install [mkcert](https://github.com/FiloSottile/mkcert)

> On macOS: `brew install mkcert`

#### Create your own certificate authority and public key infrastructure

```bash
# just once
# Install a new local CA in the system trust store
mkcert -install
```

#### Generate certificate

```bash
cd certs
mkcert \
-cert-file ide.simplism.cloud.crt \
-key-file ide.simplism.cloud.key \
simplism.cloud "*.simplism.cloud" localhost 127.0.0.1 ::1
```

#### Update hosts file

```bash
0.0.0.0 ide.simplism.cloud
```
> the URL will be https://ide.simplism.cloud:3000

### Use git
> this section is a 🚧 work in progress
 Open a terminal from the Web IDE
- Type this command `git config --global --add safe.directory /ide.simplism.cloud`
- And configure **git** (if necessary):
  ```bash
  git config --global user.name @your-handle
  git config --global user.email your@e.mail
  ```
