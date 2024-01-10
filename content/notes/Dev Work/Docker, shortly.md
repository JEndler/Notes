---
title: Some Notes about Clean Coding.
---

# Docker, shortly
1. What problems does docker solve?
2. Whats the difference between images and containers?
3. How does Docker help create applications?

# Why Docker?

> Streamline the otherwise tedious management of vm deployment.
> Consistently test and deploy your code without the hassle of configuring various environments.

## Whats the difference between virtual machines and containers?

Normally a VM fleet wpuld consist of many VMs, all managed by a hypervisor, which runs on a host OS that is installed on usual server hardware.

So basically the server has a host OS, and the virtual machines themselves have a complete operating system installed.

Other than typical VMs, containers dont have a guest OS. Inside the container you can build whatever you want. 


### Dockerfiles

Dockerfiles contain the instructions to build your docker images.
They exist just as "Dockerfile" in your Project Repo, no file extension.

*small example*:

```Dockerfile
FROM ubuntu

RUN apt update

RUN apt install something

CMD ["echo" "hello World"]
```

The **FROM** statement declares the image your image should be based on.

**RUN** is used to execute commands during the building of the image, while **CMD** is only executed once when the container is created out of the image.

You would build an run your container like so:

```bash
docker build -t someName .
docker run -d -restart unless-stopped someName
```

---

Example for Webserver:

```Dockerfile
# Use an official Python runtime as a parent image

FROM python:3

LABEL maintainer="jakob@endler.tech"

# Set environment varibles

ENV PYTHONUNBUFFERED 1

ENV DJANGO_ENV dev

RUN mkdir /app

WORKDIR /app

ADD . /app

RUN pip install poetry

RUN poetry install

EXPOSE 8018

CMD poetry run gunicorn -w 2 -b 127.0.0.1:8018 portfolio.wsgi
```

related:

[[Dev Work/Kubernetes]]


#fundamentals #docker

