# Lab 03: Docker Volumes and Persistent Data

## Objective

Learn how Docker volumes keep important files separate from replaceable containers. Prove that data survives container deletion, recreation, and an Ubuntu reboot.

## Environment

- Ubuntu Server 24.04.4 LTS
- Docker Engine
- Nginx
- Proxmox VE
- OpenSSH
- Tailscale

## Core Concept

A container runs the application, while a volume stores data that must survive.

A simple comparison:

- Container = replaceable mobile app installation
- Image = application package from the app store
- Volume = persistent application data
- `index.html` = a saved file

The container can be replaced without deleting the volume.

## Architecture

Browser → Ubuntu port `8083` → Nginx container → `/usr/share/nginx/html` → `lab03-data` volume

## Project Directory

`/home/grace/docker-lab03`

## Create the Volume

The named volume was created with:

`sudo docker volume create lab03-data`

Docker returned:

`lab03-data`

## Inspect the Volume

The volume was inspected with:

`sudo docker volume inspect lab03-data`

Important results:

- Name: `lab03-data`
- Driver: `local`
- Scope: `local`
- Mountpoint: `/var/lib/docker/volumes/lab03-data/_data`

Docker manages this storage location.

## Create the First Container

The first Nginx container was created with:

`sudo docker run -d --name lab03-nginx -p 8083:80 -v lab03-data:/usr/share/nginx/html nginx:latest`

This command:

- created a container called `lab03-nginx`;
- mapped Ubuntu port `8083` to container port `80`;
- mounted `lab03-data` into the Nginx website directory;
- used the official Nginx image.

## Default Nginx Files

The official Nginx image already contained:

- `index.html`
- `50x.html`

Because the new volume was empty, Docker copied those existing files into it when the volume was first mounted.

The files were checked with:

`sudo docker exec lab03-nginx ls -l /usr/share/nginx/html`

The default Nginx welcome page opened successfully on port `8083`.

## Add Persistent Content

The welcome page was replaced with custom content:

`sudo docker exec lab03-nginx sh -c 'echo "<h1>Grace Docker Lab 03</h1><p>This content is stored in a persistent Docker volume.</p>" > /usr/share/nginx/html/index.html'`

The browser displayed:

**Grace Docker Lab 03**

This content is stored in a persistent Docker volume.

Because `/usr/share/nginx/html` was mounted to the volume, the updated file was stored in `lab03-data`.

## Delete the Original Container

The first container was removed completely:

`sudo docker rm -f lab03-nginx`

The website went offline because no Nginx container was running.

The volume remained intact.

## Create a Replacement Container

A completely new container was created:

`sudo docker run -d --name lab03-nginx-new -p 8083:80 -v lab03-data:/usr/share/nginx/html nginx:latest`

The replacement container had a new name and a new container ID, but it used the same volume.

The custom webpage returned immediately without recreating `index.html`.

This proved:

Old container removed → volume remained → new container mounted the same volume → saved data returned

## Verify the Saved File

The file was read from inside the replacement container:

`sudo docker exec lab03-nginx-new cat /usr/share/nginx/html/index.html`

Output:

`<h1>Grace Docker Lab 03</h1><p>This content is stored in a persistent Docker volume.</p>`

## Verify the Mount

The full container configuration was inspected with:

`sudo docker inspect lab03-nginx-new`

Because the output was very large, a focused command was also used:

`sudo docker inspect --format '{{json .Mounts}}' lab03-nginx-new`

Important results:

- Type: `volume`
- Name: `lab03-data`
- Source: `/var/lib/docker/volumes/lab03-data/_data`
- Destination: `/usr/share/nginx/html`
- Read/write access: `true`

## Configure Automatic Restart

The replacement container originally had no restart policy.

It was updated with:

`sudo docker update --restart unless-stopped lab03-nginx-new`

The policy was verified with:

`sudo docker inspect --format '{{.HostConfig.RestartPolicy.Name}}' lab03-nginx-new`

Output:

`unless-stopped`

## Reboot Recovery Test

Ubuntu was rebooted.

After reconnecting, the container was checked with:

`sudo docker ps --filter name=lab03-nginx-new`

The container showed that it was running again, and the webpage was still available.

This proved:

- Restart policy brought the container back.
- Docker volume brought the saved data back.

## Why Containers Are Replaced

Containers may be replaced when:

- an application is updated;
- a security patch is released;
- configuration changes;
- deployment fails;
- a cleaner container is needed;
- the application is moved or rebuilt.

Important data should therefore not depend on the replaceable container layer.

## Important Limitation

A persistent volume is not automatically a backup.

Data can still be lost if:

- the volume is deleted;
- the server disk fails;
- files are corrupted;
- the host is destroyed;
- an administrator makes a mistake.

A separate backup and restore process is still required.

## Troubleshooting and Lessons Learned

### The default files did not come from `mkdir`

The `mkdir` command created only the ordinary project folder:

`/home/grace/docker-lab03`

The Nginx `index.html` and `50x.html` files came from the official Nginx image.

### Persistence and availability are different

- A volume protects application data.
- A restart policy restores the running service.

They solve different problems.

### `docker inspect` can return too much information

The normal inspect command displayed the container’s complete registration record.

Using `--format` made it possible to request only the mount or restart-policy information needed.

## Skills Demonstrated

- Docker named volumes
- Persistent application storage
- Volume inspection
- Nginx deployment
- Port mapping
- Container replacement
- File persistence testing
- Container mount inspection
- Restart policies
- Reboot recovery
- Docker troubleshooting
- Separation of applications and data

## Security and Privacy Notes

Persistent data must be managed responsibly.

Organizations should:

- retain only necessary data;
- follow deletion and retention policies;
- restrict access to sensitive information;
- delete or anonymize data when required;
- include backups in retention planning.

Persistence does not mean data should be kept forever.

## Result

Successfully created a Docker named volume, stored custom Nginx content inside it, removed the original container, connected a replacement container to the same volume, and verified that the data survived. The container and webpage also recovered successfully after Ubuntu rebooted.
