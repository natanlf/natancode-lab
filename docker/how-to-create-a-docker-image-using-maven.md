---
title: "How to create a Docker Image using maven?"
slug: how-to-create-a-docker-image-using-maven
date: 2024-11-28T00:15:21
categories: ["Docker", "Java", "Spring"]
tags: ["Docker", "Java", "Spring"]
---
Creating Docker images using Maven is essential to integrate the containerization process directly into the application's build and deployment pipeline, offering significant benefits for developers and DevOps teams. Let's see how to create a Docker image with Maven in Spring.

This post is a continuation of the previous one; if you have any questions, feel free to check it out: [previous post](https://natancode.com/2024/11/20/how-to-build-application-image-with-dockerfile/).

Let's practice

![](https://natancode.com/wp-content/uploads/2023/10/image-2.gif)

We can use a Maven plugin, and there is a repository that can assist us with this: [https://github.com/spotify/dockerfile-maven](https://github.com/spotify/dockerfile-maven)

![](https://natancode.com/wp-content/uploads/2024/11/image-9.png)

This way, we can copy the plugin code to paste into the project's `POM.xml` and make the necessary modifications. To avoid creating an image every time we build the project, we will use **Profiles**, allowing us to choose when to create the image.

```
	<profiles>
		<profile>
			<id>docker</id>
			<build>
				<plugins>

				</plugins>
			</build>
		</profile>
	</profiles>
```

In this way, we create the profile with the ID **docker**, and within the `<plugins>` tags, we can paste the code from the repository.

In the `<properties>` section of the `pom.xml`, we will add this property.

```
<dockerfile-maven-version>1.4.13</dockerfile-maven-version>
```

And in the `<profiles>` section, it looks like this:

```
<profiles>
		<profile>
			<id>docker</id>
			<build>
				<plugins>
					<plugin>
						<groupId>com.spotify</groupId>
						<artifactId>dockerfile-maven-plugin</artifactId>
						<version>${dockerfile-maven-version}</version>
						<executions>
							<execution>
								<id>default</id>
								<goals>
									<goal>build</goal>
									<goal>push</goal>
								</goals>
							</execution>
						</executions>
						<configuration>
							<repository>company-api</repository>
							<!--<tag>0.0.1</tag>-->
							<buildArgs>
								<JAR_FILE>${project.build.finalName}.jar</JAR_FILE>
							</buildArgs>
						</configuration>
					</plugin>
				</plugins>
			</build>
		</profile>
	</profiles>
```

In the `<repository>`, you need to specify the repository name, and in the `<tag>`, you can define the specific tag. Since we don't have any specific image, I'll comment it out, so it will create the `latest` tag by default.

In `<buildArgs>`, we specify build-time arguments, allowing us to define parameters for building the image. Here, `JAR_FILE` receives the name of the `.jar` file.

We will need to use `JAR_FILE` in the Dockerfile.

```
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

ARG JAR_FILE

COPY target/${JAR_FILE} /app/api.jar

EXPOSE 8080

CMD ["java", "-jar", "api.jar"]
```

`ARG` receives the value provided by `JAR_FILE`, so it will hold the name of the `.jar` file.

By running the following command, we can see that no Docker image was generated.

![](https://natancode.com/wp-content/uploads/2024/11/image-15.png)

To create the image, we must run the command.

```
mvn package -Pdocker
```

Note that "docker" is the ID name I used in the profile created in the pom.xml file.

![](https://natancode.com/wp-content/uploads/2024/11/image-16.png)

In this way, it's possible to create the image.

![](https://natancode.com/wp-content/uploads/2024/11/image-17.png)
