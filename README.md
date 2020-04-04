# Radio K8S
Run an Internet radio station on Kubernetes using Spotify, [Mopidy/Iris](https://github.com/jaedb/Iris) and [Icecast](https://icecast.org/).

At work we missed our office Sonos setup so I recreated the experience using a Raspberry Pi (and Raspbian), [Mopidy/Iris](https://github.com/jaedb/Iris) and [Icecast](https://icecast.org/). This repository is my work to recreate that on Kubernetes...because everything is better with more Kubernetes.

This is a work in progress at the moment but the basics work.

## To-Do
- Password protection
- HTTPS
- Not storing passwords in ConfigMaps
- Harass the Docker image maintainers into providing tags other than `latest`

## You will need
- Kubernetes with and Ingress Controller e.g. Nginx Ingress, Traefik etc - Tested on 1.16 but should work on any fairly recent version
- A Spotify Premium Account

## How to install
- Fill in your Spotify account details in `mopidy-configmap.yaml`
- Fill in your domain name in `ingress.yaml`
- Optional: Edit `mopidy-configmap.yaml` and `icecast-configmap.yaml` to change the `hackme` password used by Icecast to something a little more secure. Although only the `/listen` path of Icecast is exposed to the Internet via the Ingress so this isn't a huge security risk really...