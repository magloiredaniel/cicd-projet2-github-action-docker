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

```
name: CI/CD Workflow !!

on: push

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  build-frontend:
    runs-on: ubuntu-latest
    permissions:
      packages: write
    steps:
      - name: checkout code
        uses: actions/checkout@v4

#QEMU va nous permettre d'émuler un certains nombre de choses. Achitecture, bios, ram, etc
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3

#Buildx est ici comme on tape la commande docker build, elle va nous construire l'image.continue-on-error: 
#Buildx c'est une variante de docker build avec des fonctionnalités supplémentaires
      -  name: Set up Docker Buildx
         uses: docker/setup-buildx-action@v3

      - name: Docker meta
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/frontend:latest

      # - name: Login to Docker Hub
      #   uses: docker/login-action@v3
      #   with:
      #     username: ${{ secrets.DOCKERHUB_USERNAME }}
      #     password: ${{ secrets.DOCKERHUB_TOKEN }}

      # - name: Build and push to Docker Hub
      #   uses: docker/build-push-action@v5
      #   with:
      #     context: ./node-api
      #     push: true
      #     tags: ${{ secrets.DOCKERHUB_USERNAME }}/github-app/cicd-projet2-github-action-docker/frontend:latest
      #     secrets: |
      #       GIT_AUTH_TOKEN=${{ secrets.MYTOKEN }}
      #     cache-from: type=gha
      #     cache-to: type=gha,mode=max

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push GitHub Container Registry
        uses: docker/build-push-action@v5
        with:
          context: ./frontend
          push: true
          tags: ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/frontend:latest
          #Gestion du systeme de cache
          #Cette instruction nous permet de spécifier que nous sallons utiliser la cache de github action (gha) 
          #et mode=max pour dire qu'on mettre tout même les étapes intermédiares (les layers des images construites)
          cache-from: type=gha
          cache-to: type=gha,mode=max


  build-node-api:
    runs-on: ubuntu-latest
    permissions:
      packages: write
    steps:
      - name: checkout code
        uses: actions/checkout@v4

      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3

      -  name: Set up Docker Buildx
         uses: docker/setup-buildx-action@v3

      - name: Docker meta
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/frontend:latest

      # - name: Login to Docker Hub
      #   uses: docker/login-action@v3
      #   with:
      #     username: ${{ secrets.DOCKERHUB_USERNAME }}
      #     password: ${{ secrets.DOCKERHUB_TOKEN }}

      # - name: Build and push Docker Hub
      #   uses: docker/build-push-action@v5
      #   with:
      #     context: ./node-api
      #     push: true
      #     tags: ${{ secrets.DOCKERHUB_USERNAME }}/github-app/cicd-projet2-github-action-docker/node-api:latest
      #     secrets: |
      #       GIT_AUTH_TOKEN=${{ secrets.MYTOKEN }}
      #     cache-from: type=gha
      #     cache-to: type=gha,mode=max
          

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push GitHub Container Registry
        uses: docker/build-push-action@v5
        with:
          context: ./node-api
          push: true
          tags: ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/node-api:latest
          cache-from: type=gha
          cache-to: type=gha,mode=max



  # build-frontend-and-node-api:
  #   runs-on: ubuntu-latest
  #   strategy:
  #     matrix:
  #       include:
  #         - context_name: ./frontend
  #           image_name_ghcr: ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/frontend
  #           image_name_hub: ${{ secrets.DOCKERHUB_USERNAME }}/github-app/cicd-projet2-github-action-docker/frontend
  #         - context_name: ./node-api
  #           image_name_ghcr: ghcr.io/magloiredaniel/cicd-projet2-github-action-docker/node-ap
  #           image_name_hub: ${{ secrets.DOCKERHUB_USERNAME }}/github-app/cicd-projet2-github-action-docker/node-ap
  #   permissions:
  #     packages: write
  #   steps:
  #     - name: checkout code
  #       uses: actions/checkout@v4

  #     - name: Set up QEMU
  #       uses: docker/setup-qemu-action@v3

  #     -  name: Set up Docker Buildx
  #        uses: docker/setup-buildx-action@v3

  #      - name: Docker meta
  #        id: meta
  #        uses: docker/metadata-action@v5
  #        with:
  #          images: ${{ matrix.image_name_ghcr }}

  #     - name: Login to Docker Hub
  #       uses: docker/login-action@v3
  #       with:
  #         username: ${{ secrets.DOCKERHUB_USERNAME }}
  #         password: ${{ secrets.DOCKERHUB_TOKEN }}

  #     - name: Build and push Docker Hub
  #       uses: docker/build-push-action@v5
  #       with:
  #         context: ${{ matrix.context_name }}
  #         push: true
  #         tags: ${{ matrix.image_name_ghcr }}:latest
  #         cache-from: type=gha
  #         cache-to: type=gha,mode=max

  #     - name: Login to GitHub Container Registry
  #       uses: docker/login-action@v3
  #       with:
  #         registry: ghcr.io
  #         username: ${{ github.actor }}
  #         password: ${{ secrets.GITHUB_TOKEN }}

  #     - name: Build and push GitHub Container Registry
  #       uses: docker/build-push-action@v5
  #       with:
  #         context: ${{ matrix.context_name }}
  #         push: true
  #         tags: ${{ matrix.image_name_ghcr }}:latest
  #         cache-from: type=gha
  #         cache-to: type=gha,mode=max
```