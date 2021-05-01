# Caddy Demo for serving static files

## Using Ubuntu

Multipass is like a local cloud, get it on https://multipass.run

```bash
VM_NAME=${VM_NAME:-'caddy1'}
VM_IMAGE=${VM_IMAGE:-'21.14'}
VM_MEM=${VM_MEM:-'1G'}
VM_DISK=${VM_DISK:-'1G'}

multipass launch --name "${VM_NAME}" --mem "${VM_MEM}" --disk "${VM_DISK}" "${VM_IMAGE}"

multipass mount -u "$(id -u)":1001 -g "$(id -g)":1001 "$(pwd)" "${VM_NAME}":/home/ubuntu/files

multipass exec "${VM_NAME}" -- bash -c 'sudo apt-get update && sudo apt-get upgrade -y'
```

## Install Caddy

https://caddyserver.com/docs/install

```bash
multipass exec "${VM_NAME}" -- bash -c 'sudo apt-get install -y debian-keyring debian-archive-keyring apt-transport-https'
multipass exec "${VM_NAME}" -- bash -c 'curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo apt-key add -'
multipass exec "${VM_NAME}" -- bash -c 'curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee -a /etc/apt/sources.list.d/caddy-stable.list'
multipass exec "${VM_NAME}" -- bash -c 'sudo apt-get update'
multipass exec "${VM_NAME}" -- bash -c 'sudo apt-get install -y caddy'
```

## Getting Started with Caddy

https://caddyserver.com/docs/getting-started

```bash
multipass exec "${VM_NAME}" -- bash -c 'sudo systemctl enable --now caddy.service'

curl $(multipass list | grep "${VM_NAME}" | head -n 1 | awk '{print $3}')

```

## Serving Content with Caddy

https://caddyserver.com/docs/quick-starts/caddyfile

```bash
mkdir -p $(pwd)/var/www/html

multipass exec "${VM_NAME}" -- bash -c 'sudo mkdir -p /var/www/html'
multipass exec "${VM_NAME}" -- bash -c 'sudo sed -i 's#/usr/share/caddy#/var/www/html#g' /etc/caddy/Caddyfile'

multipass mount -u "$(id -u)":1 -g "$(id -g)":1 "$(pwd)/var/www/html" "${VM_NAME}":/var/www/html

multipass exec "${VM_NAME}" -- bash -c 'sudo systemctl restart caddy.service'

```
