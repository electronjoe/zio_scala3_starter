# ZIO Scala3 Template with VS Code Containers

This repository is a Github Template for [ZIO](https://zio.dev/) library use in [Scala 3](https://dotty.epfl.ch/).  It uses VS Code Devcontainer configuration to support ~zero effort environmental setup with just two dependencies: `Visual Studio Code` with the `Remote Container` extension (somewhat a misnomer, can be local use also), and `Docker`. See below for details. Presently this template employs the following:

- [hseeberger/scala-sbt](https://github.com/hseeberger/scala-sbt) with
  - `OpenJDK 11.0.10`
  - `SBT 1.5.0`
  - `Scala 3.0.0-RC2`
- `ZIO 1.0.6`

Big thank you to [hseeberger/scala-sbt](https://github.com/hseeberger/scala-sbt), whose scala-sbt Dockerfile I use as a basis for this Template (then layering on VS Code Remote Devcontainer support in this repo's Dockerfile).

## Origin Story

With the release of Scala 3, and being very curious about the [ZIO](https://zio.dev/) library, I wanted to explore and learn.  I created this Template after having some trouble following the Scala 3 `Getting Started` options - ultimately I was successful but it was not a smooth experience.  In my professional life I've found `VS Code's` `Remote - Containers` extension to be excellent for ~one-click environmental spin-up, minimal IDE support and and github repository integration. So I created a GH Template with this tool stack.

## Using this Template

1. Create your own repo using this Template ([Creating a repository from a template](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-repository-from-a-template))
1. Install [Visual Studio Code](https://code.visualstudio.com/download)
1. Install [Docker](https://docs.docker.com/get-docker/)
1. Open Visual Studio Code and [install](https://code.visualstudio.com/docs/editor/extension-gallery) the [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension
1. Hit `F1` to get the `Command Pallete` and type `Remote-Containers: Clone Repository in Container Volume...'
1. Paste the URL of your Github repository into the box (e.g. `https://github.com/electronjoe/zio_scala3_vscode_template` but substitute your username and repo)
1. Hit enter, then when prompted select `Create Unique Volume` in Docker, hit enter
1. Now be patient, much has to happen. You should see a `Starting Devcontainer` Notification (you can click to monitor logs).
    1. VS Code will clone the repo into a Docker Mount
    1. VS Code will fetch the base Docker container (~600 MB [github.com/hseeberger/scala-sbt](hseeberger/scala-sbt))
    1. VS Code will use docker build to add VS code Container support
    1. VS Code will auto-install in container plugins incl [Metals](https://scalameta.org/metals/docs/editors/vscode.html)
1. Once the source code appears, you should be presented with a notification at bottom right `New sbt workspace detected, would you like to import the build?` -> Click `Import build` (click bell icon at bottom right if you missed it)
    1. At this point you can monitor progress `View->Output` and from the drop-down select `Metals`
    1. Be patient... very patient...
    1. sbt fetches dependencies, metals and boop get configured - you may see error messages in `Metals` Output, keep waiting
1. You will know all is complete when, if you open `src/main/scala/Main.scala` and click on e.g. the `myAppLogic` symbol a tooltip giving type information is displayed (`val myAppLogic: zio.ZIO[Console, java.io.Exception, Unit]`)

You can test run Main.scala by opening a new terminal (`Terminal` -> `New Terminal`) and typing `sbt run`. You should observe:

```shell
vscode ➜ /workspaces/zio_scala3_starter (main ✗) $ sbt run
copying runtime jar...
[info] welcome to sbt 1.5.0-RC2 (Oracle Corporation Java 11.0.10)
[info] loading settings for project zio_scala3_starter-build-build from metals.sbt ...
[info] loading project definition from /workspaces/zio_scala3_starter/project/project
[info] loading settings for project zio_scala3_starter-build from metals.sbt ...
[info] loading project definition from /workspaces/zio_scala3_starter/project
[success] Generated .bloop/zio_scala3_starter-build.json
[success] Total time: 1 s, completed Apr 16, 2021, 10:38:00 AM
[info] loading settings for project root from build.sbt ...
[info] set current project to scala3-simple (in build file:/workspaces/zio_scala3_starter/)
[info] compiling 1 Scala source to /workspaces/zio_scala3_starter/target/scala-3.0.0-RC2/classes ...
[info] running MyApp 
Hello! What is your name?
scott
Hello, scott, welcome to ZIO!
[success] Total time: 33 s, completed Apr 16, 2021, 10:38:34 AM
```

## Issues

It appears to me that Metals is re-downloading sbt and not using the sbt provided by the [hseeberger/scala-sbt](https://github.com/hseeberger/scala-sbt) base Docker layer. This is incredibly inefficient and should be remedied.

## Potential Future Work

Offer GraalVM native builds ([github.com/hseeberger/scala-sbt](https://github.com/hseeberger/scala-sbt) has a docker build for this, but it is not in a favorable base OS for the VS Code devcontainer overlay conviguration - at least not in my experimentation so far).


```shell
docker run --mount source=.,target=/home/sbtuser -it --rm -u sbtuser -w /home/sbtuser hseeberger/scala-sbt:latest /bin/sh
```