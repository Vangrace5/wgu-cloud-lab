# Lab 04: Docker Bind Mounts and Live File Changes

## Objective

Learn how to connect a normal Ubuntu folder directly to a Docker container, then prove that:

- file changes appear immediately;
- the container can be limited to read-only access;
- host files survive container deletion;
- a replacement container can reuse the same folder;
- the service can restart after reboot.

## Environment

- Ubuntu Server 24.04.4 LTS
- Docker Engine
- Nginx
- Proxmox VE
- OpenSSH
- Tailscale

## Core Concept

A bind mount connects a real folder on Ubuntu to a folder inside a container.

Ubuntu source folder:

`/home/grace/docker-lab04/website`

Container destination:

`/usr/share/nginx/html`

Simple comparison:

- Ubuntu folder = shared company folder
- Container = worker using that folder
- Bind mount = connection between them
- Read-only access = worker may view files but cannot change them

## Architecture

Browser → Ubuntu port `8084` → Nginx container → bind mount → Ubuntu website folder

## Create the Host Folder

The project folder was created with:

`mkdir -p ~/docker-lab04/website`

The folder was opened with:

`cd ~/docker-lab04/website`

The location was confirmed with:

`pwd`

Output:

`/home/grace/docker-lab04/website`

## Create the Webpage

The webpage was created with:

`nano index.html`

The final content was:

`<!DOCTYPE html>`

`<html>`

`<head>`

`  <title>Docker Bind Mount Lab 04</title>`

`</head>`

`<body>`

`  <h1>Grace Docker Bind Mount Lab 04</h1>`

`  <p>This page changed live without rebuilding the Docker image or replacing the container.</p>`

`</body>`

`</html>`

The file was checked with:

`cat index.html`

## Create the First Container

The first container was created with:

`sudo docker run -d --name lab04-bind-nginx -p 8084:80 --mount type=bind,source=/home/grace/docker-lab04/website,target=/usr/share/nginx/html,readonly nginx:latest`

This command:

- created the container;
- mapped Ubuntu port `8084` to Nginx port `80`;
- mounted the Ubuntu website folder into the container;
- made the mount read-only;
- used the official Nginx image.

The webpage opened successfully.

## Live Change Test

The host file was edited with:

`nano ~/docker-lab04/website/index.html`

After saving and refreshing the browser, the new text appeared immediately.

No Docker image rebuild was required.

No container replacement was required.

This proved that Nginx was reading the live Ubuntu file through the bind mount.

## Read-Only Test

The container was asked to create a file inside the mounted folder:

`sudo docker exec lab04-bind-nginx sh -c 'echo "container test" > /usr/share/nginx/html/container-test.txt'`

Docker returned:

`Read-only file system`

This proved that the container could read and serve the files but could not change them.

## Inspect the Bind Mount

The mount was inspected with:

`sudo docker inspect --format '{{json .Mounts}}' lab04-bind-nginx`

The result confirmed:

- Type: `bind`
- Source: `/home/grace/docker-lab04/website`
- Destination: `/usr/share/nginx/html`
- Read/write: `false`

## Restart Policy

The restart policy was added with:

`sudo docker update --restart unless-stopped lab04-bind-nginx`

It was verified with:

`sudo docker inspect --format '{{.HostConfig.RestartPolicy.Name}}' lab04-bind-nginx`

Output:

`unless-stopped`

## Reboot Recovery Test

Ubuntu was rebooted.

The container was checked with:

`sudo docker ps --filter name=lab04-bind-nginx`

The container returned automatically, and the webpage worked again.

## Delete the First Container

The first container was removed with:

`sudo docker rm -f lab04-bind-nginx`

The webpage went offline because Nginx was no longer running.

The host file was still present and was checked with:

`cat ~/docker-lab04/website/index.html`

This proved that the webpage belonged to the Ubuntu folder, not to the container.

## Create the Replacement Container

A new container was created with:

`sudo docker run -d --name lab04-bind-nginx-new -p 8084:80 --mount type=bind,source=/home/grace/docker-lab04/website,target=/usr/share/nginx/html,readonly --restart unless-stopped nginx:latest`

The replacement container used:

- a new name;
- a new container ID;
- the same Ubuntu folder;
- the same port;
- the same read-only mount;
- the restart policy.

The same webpage returned immediately.

## Final Verification

The replacement container was inspected with:

`sudo docker inspect --format 'Mount={{json .Mounts}} Restart={{.HostConfig.RestartPolicy.Name}}' lab04-bind-nginx-new`

The output confirmed:

- bind mount type;
- correct source folder;
- correct destination folder;
- `RW=false`;
- `Restart=unless-stopped`.

## Named Volume Compared with Bind Mount

A named volume is managed by Docker.

Example:

`lab03-data`

A bind mount uses an exact folder chosen by the administrator.

Example:

`/home/grace/docker-lab04/website`

Named volumes are useful for application-managed persistent data.

Bind mounts are useful for:

- development;
- live source-code editing;
- configuration files;
- host-managed website files;
- logs and exported files.

## Security Lesson

Bind mounts can expose important host files to containers.

Containers should receive access only to the exact folder they need.

Read-only access should be used when the container does not need to edit files.

Risky examples include mounting:

- `/`
- `/etc`
- `/home/grace`
- Docker internal directories

The security principle is:

**Grant only the smallest amount of access required.**

## Troubleshooting

The first command mistakenly used:

`type+bind`

The correct syntax was:

`type=bind`

Docker template fields were also case-sensitive.

`.Hostconfig` failed.

`.HostConfig` worked.

Long commands wrapped across multiple terminal lines because the screen was split. The commands themselves were not broken.

## Skills Demonstrated

- Docker bind mounts
- Host-folder management
- Live file changes
- Read-only mounts
- Nginx deployment
- Container replacement
- Host-file persistence
- Restart policies
- Reboot recovery
- Docker inspection
- Linux file editing
- Least-privilege security
- Troubleshooting command syntax

## Result

Successfully mounted a normal Ubuntu folder into an Nginx container, changed the webpage live, blocked container writes, removed the original container, created a replacement container, and restored the same webpage from the original host folder.
