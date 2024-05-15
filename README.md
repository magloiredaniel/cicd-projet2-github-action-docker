# Partie CI

![statut](https://github.com/magloiredaniel/cicd-projet1-github-action/actions/workflows/main.yml/badge.svg)

![statut](https://github.com/magloiredaniel/cicd-projet1-github-action/actions/workflows/main.yml/badge.svg?event=push&branch=master)

[![codecov](https://codecov.io/gh/magloiredaniel/cicd-projet1-github-action/graph/badge.svg?token=LJ3ZH4N116)](https://codecov.io/gh/magloiredaniel/cicd-projet1-github-action)

# cypress-io/github-action [![Action status][ci-badge]][ci-workflow] [![cypress][cloud-badge]][cloud-project] [![renovate-app badge][renovate-badge]][renovate-bot]

[Here is CodeCov Account](https://about.codecov.io/)

```
$ docker login ghcr.io
Username: magloiredaniel
Password:
Login Succeeded
```

On builder l'image de la partie frontend

```
$ docker build -t ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/frontend .
```

Si vous avec une erreur liée au manque du sass, installez avec la commande ci-dessous

```
npm install -D sass
```
Puis relancer la build de l'image

On va push notre image frontend dans le repo Packages de github

```
docker push ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/frontend
```
On builder l'image de la partie backend (node-api)

```
$ docker build -t ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/node-api .
```
On va push notre image backend dans le repo Packages de github

```
docker push ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/node-api
```