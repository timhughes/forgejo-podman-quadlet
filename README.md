
# Forgejo on Podman with Quadlet

## Table of Contents

- [Description](#description)
- [Installation](#installation)
- [More Reading](#more-reading)

## Description

A brief description of your project goes here.

## Installation

Create self signed cert

```bash
openssl req -x509 -sha256 -nodes -days 365 \
    -newkey rsa:4096 -keyout certificate.key -out certificate.pem
```

Load them into podman

```bash
kubectl create secret generic \
    --from-file=certificate.key \
    --from-file=certificate.pem \
    envoy-certificates \
    --dry-run=client \
    -o yaml | \
    podman kube play -
```

Create the database root and user passwords

```bash
DATABASE_ROOT_PASSWORD=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 16)

kubectl create secret generic \
    --from-literal=password="${DATABASE_ROOT_PASSWORD}" \
    forgejo-root-database-password-kube \
    --dry-run=client \
    -o yaml | \
    podman kube play -


DATABASE_FORGEJO_PASSWORD=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 16)

kubectl create secret generic \
    --from-literal=password="${DATABASE_FORGEJO_PASSWORD}" \
    forgejo-database-password-kube \
    --dry-run=client \
    -o yaml | \
    podman kube play -
```

Copy the files into position

```bash
mkdir -p $HOME/.config/containers/systemd/
cp envoy-proxy-configmap.yml forgejo* $HOME/.config/containers/systemd/
```

```bash
systemctl --user daemon-reload
systemctl --user start forgejo-database.service
systemctl --user start forgejo.service
```

```bash
journalctl --user -xeu forgejo-database.service

journalctl --user -xeu forgejo.service
```

## More Reading

- <https://www.redhat.com/en/blog/quadlet-podman>
- <https://www.redhat.com/en/blog/multi-container-application-podman-quadlet>
