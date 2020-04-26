# Radio K8S
Run an Internet radio station on Kubernetes using Spotify, [Mopidy/Iris](https://github.com/jaedb/Iris) and [Icecast](https://icecast.org/).

When we all moved to working from home, we missed our office Sonos system, which allowed anyone to inflict their music taste on the whole office. Initially I recreated the experience using a Raspberry Pi (and Raspbian), [Mopidy/Iris](https://github.com/jaedb/Iris) and [Icecast](https://icecast.org/). However this was fiddly to set up and maintain. This is the second incarnation using Kubernetes, so anyone can set something similar up for their friends on colleagues.

**This is never going to be "production grade", and is intended as a bit of fun only!**

## You will need
- A Spotify Premium Account
- Kubernetes with an Ingress Controller e.g. Traefik
  - Tested on Kubernetes 1.16 but it should work with any fairly recent version
  - The resources required are tiny; my cluster is a [Civo Cloud](https://www.civo.com/kube100) k3s cluster on a single node with 1 core and 2GB RAM. However no Raspberry Pi support yet, sorry! (see [To-Do](#to-do))

## How to install
- Fill in your Spotify account details in `mopidy-configmap.yaml`
- Fill in your domain name in `ingress.yaml`
- Optional: Edit `mopidy-configmap.yaml` and `icecast-configmap.yaml` to change the `hackme` password used by Icecast to something a little more secure. Although only the `/listen` path of Icecast is exposed to the Internet via the Ingress so this isn't a huge security risk really...
- Run `kubectl apply -f .`

## HTTPS and Password Protection
I am using Traefik as my Ingress Controller, installed using the [stable/traefik](https://github.com/helm/charts/tree/master/stable/traefik) Helm Chart. Conveniently this supports basic authentication and HTTPS using Let's Encrypt with a bit of config. You should follow the documention for that project but I've committed an example Helm [values file](./traefik-values.yaml.example) and [Ingress configuration](./ingress.yaml) which should help to get you up and running.

## ARM64 Architecture support
The Docker images used only support the amd64 architecture. I've built some arm64 images you can use instead if you want to run on a Raspberry Pi ([DockerFiles here](./docker)):

- cablespaghetti/icecast:2.4.4-alpine
- cablespaghetti/iris:3.47.0-1

## To-Do
- Stop storing passwords in ConfigMaps.
- Harass the Docker image maintainers into providing tags other than `latest`.
- Maybe make this a Helm chart for easier configuration.
- Stream silence when music is paused/stopped rather than having no stream.
- Fix resume when track is paused. Currently you must restart the track.
