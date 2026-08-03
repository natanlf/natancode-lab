---
title: "Docker: Essential Commands Every Developer Should Know"
slug: docker-essential-commands-every-developer-should-know
date: 2025-07-09T14:14:49
categories: ["Docker"]
tags: ["Docker"]
---
Docker has revolutionized the way developers package, distribute, and run applications. With it, you can create portable and standardized environments with incredible ease. To make the most of this powerful tool, it's essential to master some basic commands that are part of daily work with containers, images, and networks. In this post, we’ll explore the key Docker commands, explaining what they do and giving practical examples so you can immediately apply them to your workflow.

![](https://natancode.com/wp-content/uploads/2025/04/giphy.gif)

🔍 Searching on DockerHub

`docker search`: allows you to search images on DockerHub. You can view available options using the command below:

```
docker search --help
```

For example, to search for a Node image:

```
docker search node
```

✅ Verifying Docker Is Working

```
docker --version
docker info
```

📥 Downloading an Image

```
docker pull nginx
```

`nginx` is a popular image available on DockerHub.

🚀 Running a Container

Basic run:

```
docker run nginx
```

You can also specify a name for the container:

```
docker run --name server nginx
```

Running in Detached Mode

```
docker run -d nginx
```

The **detached mode** in Docker allows you to run a container in the background, meaning it keeps running independently of your terminal.

Run and map a port:

```
docker run -d -p 8080:80 nginx
```

Here, `8080` is the port on your machine (host), and `80` is the internal port of the container.

📋 Listing Containers

Running containers only:

```
docker ps
```

All containers (including stopped):

```
docker ps -a
```

🛑 Stopping and Removing Containers

Stop a container:

```
docker stop <container_id>
```

Remove a container:

```
docker rm <container_id>
```

You can use the container's name instead of the ID.

```
docker rm <container_name>
```

📦 Listing Downloaded Images

```
docker images
```

❌ Removing an Image

```
docker rmi <image_id>
```

📄 Viewing Container Logs

```
docker logs <container_id>
```

🧹 Cleaning Up Resources

Remove all stopped containers, unused networks, dangling images, and build cache:

```
docker system prune
```

Conclusion

Mastering the essential Docker commands is a fundamental step for anyone looking to build modern, scalable, and portable applications. From building an image to running and managing containers, each command gives you more control and efficiency in both development and production. With practice and curiosity, you’ll be able to set up complex environments with ease, reduce errors, and boost productivity. Keep exploring and deepening your knowledge to make the most of this powerful container platform.
