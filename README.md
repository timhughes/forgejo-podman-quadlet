
# README

This is a basic README file for your project.

## Table of Contents

* [Description](#description)
* [Features](#features)
* [Installation](#installation)
* [Usage](#usage)
* [Contributing](#contributing)
* [License](#license)

## Description

A brief description of your project goes here.

## Features

List the main features of your project here.

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

```bash
cp envoy-proxy-configmap.yml forgejo* $HOME/.config/containers/systemd/
```

```bash
systemctl --user daemon-reload
systemctl --user start forgejo-database.service
systemctl --user start forgejo.service
```bash
journalctl --user -xeu forgejo-database.service
```

```bash
journalctl --user -xeu forgejo.service
```

### Requirements

List any dependencies required to run your project.

## Usage

Information on how to use your project goes here.

### Example Use Cases

Provide examples of how to use your project in different scenarios.

## Contributing

Guidelines for contributing to your project go here.

### Pull Request Guidelines

Explain the process for submitting a pull request.

## License

The license under which your project is released.
