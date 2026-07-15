# Lab 02: Docker, Custom Nginx Image, and Docker Compose

## Objective

Learn how Docker packages and runs applications in containers, then build and verify a persistent Nginx web application using a Dockerfile and Docker Compose.

## Environment

- Ubuntu Server 24.04.4 LTS
- Proxmox VE
- Docker Engine
- Docker CLI
- Docker Buildx
- Docker Compose
- Nginx
- Tailscale
- OpenSSH

## Starting Architecture

Ubuntu Server VM  
├── Nginx installed directly on Ubuntu  
├── Docker Engine  
└── Tailscale and SSH for remote management

## Docker Concepts Practiced

### Image

A Docker image is a reusable application blueprint.

Examples used in this lab:

- `hello-world`
- `nginx`
- `grace-nginx-lab02`
- `docker-lab02-web`

### Container

A container is a running instance created from an image.

Containers used in this lab:

- `docker-nginx`
- `grace-nginx-lab02`
- `grace-compose-web`

### Dockerfile

A Dockerfile is a text-based recipe Docker follows to build an image.

The Dockerfile used in this lab contained:

`FROM nginx:latest`

`COPY index.html /usr/share/nginx/html/index.html`

This started with the official Nginx image and copied my custom webpage into the image.

### Port Mapping

Docker port mapping connected ports on Ubuntu to ports inside containers.

- Ubuntu port `8080` mapped to container port `80`
- Ubuntu port `8081` mapped to container port `80`
- Ubuntu port `8082` mapped to container port `80`

This allowed multiple Nginx services to run on the same Ubuntu server without competing for the same host port.

### Docker Compose

Docker Compose describes an application in a YAML file.

The Compose project defined:

- how the image should be built;
- the container name;
- the port mapping;
- the restart policy;
- the private Docker network.

## Project Files

The project directory was:

`/home/grace/docker-lab02`

It contained:

- `index.html`
- `Dockerfile`
- `compose.yaml`

## Custom Webpage

The webpage displayed:

**Grace Docker Lab 02**

This page was built outside the container and packaged into a Docker image.

## Work Completed

1. Verified Docker was not already installed.
2. Added Docker’s official Ubuntu repository and signing key.
3. Installed Docker Engine, Docker CLI, containerd, Buildx, and Docker Compose.
4. Confirmed the Docker service was active.
5. Ran the `hello-world` test container.
6. Listed all containers and confirmed `hello-world` exited successfully with status code `0`.
7. Ran the official Nginx image as a long-running container.
8. Published container port `80` through Ubuntu port `8080`.
9. Verified the Nginx page remotely through Tailscale.
10. Inspected the container logs.
11. Opened a shell inside the running container.
12. Confirmed the Nginx container was based on Debian 13.
13. Located the Nginx website files inside the container.
14. Created a project directory outside the container.
15. Created a custom `index.html` webpage.
16. Created a Dockerfile.
17. Built a custom Docker image named `grace-nginx-lab02`.
18. Ran the custom image on Ubuntu port `8081`.
19. Verified the custom webpage in a browser.
20. Created a Docker Compose configuration.
21. Validated the Compose file with `docker compose config`.
22. Started the Compose project on Ubuntu port `8082`.
23. Stopped and removed the Compose application with `docker compose down`.
24. Recreated the application using `docker compose up -d`.
25. Rebooted Ubuntu.
26. Verified Docker and all containers restarted automatically.
27. Confirmed the custom webpage remained available after reboot.
28. Installed all available Ubuntu security updates.

## Dockerfile

The Dockerfile used:

`FROM nginx:latest`

`COPY index.html /usr/share/nginx/html/index.html`

## Compose Configuration

The Compose file defined one service called `web`.

It:

- built the image from the current directory;
- named the container `grace-compose-web`;
- mapped port `8082` to container port `80`;
- used the restart policy `unless-stopped`.

## Verification

The Docker service was confirmed active with:

`systemctl is-active docker`

The running containers were checked with:

`sudo docker ps`

The Compose-managed service was checked with:

`sudo docker compose ps`

The following pages were verified:

- Direct Ubuntu Nginx on port `80`
- Official Docker Nginx container on port `8080`
- Custom-image container on port `8081`
- Docker Compose application on port `8082`

The Compose-managed container remained active after Ubuntu rebooted.

## Troubleshooting and Lessons Learned

### Container name conflict

Docker reported that the name `docker-nginx` was already in use.

The existing container was inspected instead of being deleted blindly.

Command used:

`sudo docker ps -a --filter name=docker-nginx`

### Port conflicts

The Ubuntu Nginx service already used port `80`.

Different Docker containers therefore used ports `8080`, `8081`, and `8082`.

### Typing mistakes

Several command and file errors were caught through verification:

- lowercase `l` was confused with the number `1`;
- `/dev/null` was mistyped;
- the Dockerfile destination was accidentally written as `.index.html`;
- a typo in `index.html` was corrected;
- YAML indentation was corrected to use spaces instead of tabs.

These mistakes demonstrated why commands and configuration files should be inspected before continuing.

### Container filesystem changes are temporary

Editing a page directly inside a running container was useful for learning, but those changes would be lost if the container were deleted and recreated.

The improved approach was:

1. Create source files outside the container.
2. Build a custom Docker image.
3. Run containers from that reusable image.
4. Define the complete application with Docker Compose.

## Skills Demonstrated

- Docker installation
- Linux package repository configuration
- Container image management
- Container lifecycle management
- Docker Hub image pulls
- Port mapping
- Container logs
- Interactive container shells
- Dockerfile creation
- Custom image building
- Build caching
- Docker Compose
- YAML configuration
- Docker networking
- Restart policies
- Reboot-persistence testing
- Linux security patching
- Troubleshooting and verification

## Security Notes

This repository does not publish:

- passwords;
- authentication keys;
- private IP addresses;
- Tailscale IP addresses;
- email addresses;
- service tags;
- private network details.

## Result

Successfully installed Docker, ran official and custom Nginx containers, built a reusable custom image, deployed the application with Docker Compose, and verified that the containerized application recovered automatically after reboot.
