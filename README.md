# docker-groovy-liberica
## Difference from the official Dockerfile

Use [liberica-openjdk-alpine-musl](https://hub.docker.com/r/bellsoft/liberica-openjdk-alpine-musl) as base image.

## Supported tags and respective Dockerfile links

* [latest](https://github.com/kingwaygroup/docker-groovy-liberica/blob/master/latest/Dockerfile)

## What is Groovy?

[Apache Groovy](http://groovy-lang.org/) is a powerful, optionally typed and dynamic language, with static-typing and static compilation capabilities, for the Java platform aimed at improving developer productivity thanks to a concise, familiar and easy to learn syntax. It integrates smoothly with any Java program, and immediately delivers to your application powerful features, including scripting capabilities, Domain-Specific Language authoring, runtime and compile-time meta-programming and functional programming.

## How to use this image

Note that if you are mounting a volume and the uid running Docker is not _1000_, you should run as user _root_ (`-u root`).

### Starting Groovysh

`docker run -it --rm groovy-liberica:latest`

### Running a Groovy script

#### Bash/Zsh

`docker run --rm -v "$PWD":/home/groovy/scripts -w /home/groovy/scripts groovy-liberica:latest groovy <script> <script-args>`

#### PowerShell

`docker run --rm -v "${pwd}:/home/groovy/scripts" -w /home/groovy/scripts groovy-liberica:latest groovy <script> <script-args>`

#### Windows CMD

`docker run --rm -v "%cd%:/home/groovy/scripts" -w /home/groovy/scripts groovy-liberica:latest groovy <script> <script-args>`

### Reusing the Grapes cache

The local Grapes cache can be reused across containers by creating a volume and mounting it in */home/groovy/.groovy/grapes*.

```
docker volume create --name grapes-cache
docker run --rm -it -v grapes-cache:/home/groovy/.groovy/grapes groovy-liberica:latest
```

Note when running as another user (other than _groovy_ or _root_), you will need to tell Groovy what home to use for Grapes with `-D user.home=/home/groovy`.

Alternatively, you can specify this through the Grapes config, using `-D grape.config=/home/groovy/scripts/grapesConfig.xml` and

```xml
<!-- same thing as https://github.com/apache/groovy/blob/master/src/resources/groovy/grape/defaultGrapeConfig.xml, but with ${user.home} replaced with /home/groovy -->
<ivysettings>
  <settings defaultResolver="downloadGrapes"/>
  <resolvers>
    <chain name="downloadGrapes" returnFirst="true">
      <filesystem name="cachedGrapes">
        <ivy pattern="/home/groovy/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
        <artifact pattern="/home/groovy/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
      </filesystem>
      <ibiblio name="localm2" root="/home/groovy/.m2/repository/" checkmodified="true" changingPattern=".*" changingMatcher="regexp" m2compatible="true"/>
      <ibiblio name="jcenter" root="https://jcenter.bintray.com/" m2compatible="true"/>
      <ibiblio name="ibiblio" m2compatible="true"/>
    </chain>
  </resolvers>
</ivysettings>
```
