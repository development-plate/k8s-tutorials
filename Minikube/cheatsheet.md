# Minikube Cheat Sheet

Author: Michael Plate

## Install

Download 

`curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb`

Install 

`sudo dpkg -i minikube_latest_amd64.deb`

## Cluster

Start minikube cluster with given profile name.

`minikube start -p <profile-name>`

Halt the cluster.

`minikube stop`

Delete the cluster.

`minikube delete`

Upgrade your cluster.

`minikube start --kubernetes-version=latest`

Show the current active profile.

`minikube profile`

List all profiles.

`minikube profile list`

Switch the active profile.

`minikube profile <profile-name>`

## Addons

List of Addons

`minikube addons list`

Enable portainer

`minikube addons enable portainer`

Enable an addon at the cluster startup time.

`minikube start --addons <addon-name>`

If an addon exposes a browser endpoint, you can quickly open it with.

`minikube addons open <addon-name>`

Disable an addon.

`minikube addons disable <addon-name>`

## Dashboard

`minikube dashboard`