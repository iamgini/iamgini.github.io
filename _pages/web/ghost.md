---
layout: post
title: Ghost
author: gini
categories: [ ghost ]
# image: "assets/images/2020/infrastructure.jpg"
tags: [web, ghost]
permalink: /ghost
featured: false
hidden: false
titleshort: ghost
---


## Ghost in Container

$ podman run -d --name some-ghost ghost
$ podman run -d -p 3001:2368 -e url=http://localhost:3001  --name some-ghost ghost

### Podman compose

```yaml
version: '3.1'

services:

  ghost:
    image: ghost:1-alpine
    restart: always
    ports:
      - 8080:2368
    environment:
      # see https://docs.ghost.org/docs/config#section-running-ghost-with-config-env-variables
      database__client: mysql
      database__connection__host: db
      database__connection__user: root
      database__connection__password: example
      database__connection__database: ghost

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
```