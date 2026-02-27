# simple-java-docker
JAVA APP – DOCKERIZED FROM SCRATCH

This project demonstrates how a simple Java application is containerized
using Docker, with a strong focus on understanding Docker image layers
and build mechanics.

  -------------------
  PROJECT STRUCTURE
  -------------------

. ├── Dockerfile ├── src/ └── README.txt

  ------------------------
  HOW TO BUILD THE IMAGE
  ------------------------

docker build -t java-app .

Run the container:

docker run java-app

  ----------------------------------
  WHAT HAPPENS DURING docker build
  ----------------------------------

When you run:

docker build -t java-app .

Docker does NOT simply “build an image”. It creates a stack of immutable
layers.

Each Dockerfile instruction creates a new layer.

Let’s break it down.

1) FROM

Example: FROM openjdk:17-jdk-alpine

-   Pulls the base image from Docker Hub (if not present locally)
-   Creates the base layer
-   Every other layer builds on top of this

Without FROM, nothing exists.

2) WORKDIR

Example: WORKDIR /app

-   Sets working directory inside the container
-   Creates a new metadata layer
-   All following commands execute relative to this path

3) COPY

Example: COPY . .

-   Copies files from local machine into the image
-   Creates a new layer
-   If files change, Docker cache breaks from this step onward

This instruction directly affects build performance and caching
efficiency.

4) RUN

Example: RUN javac Main.java

-   Executes commands during image build
-   Creates a new layer containing the result

Important: If you run 

RUN apt update RUN apt install curl

Creates TWO layers.

RUN apt update && apt install curl

Creates ONE layer.

Fewer layers = smaller image size and better optimization.

5) CMD

Example: CMD [“java”, “Main”]

-   Sets default runtime command
-   Does NOT execute during build
-   Does NOT create a filesystem layer
-   Only sets image metadata

CMD runs when container starts, not when image builds.

  ------------------
  LAYER EXPERIMENT
  ------------------

After building:

docker build -t java-app .

Run:

docker history java-app

Example output:

IMAGE CREATED CREATED BY 2 minutes ago CMD [“java” “Main”] 2 minutes ago
RUN javac Main.java 2 minutes ago COPY . . 2 minutes ago WORKDIR /app 3
days ago FROM openjdk:17-jdk-alpine

What this shows:

-   Each instruction becomes a separate layer
-   Layers are stacked
-   Docker caches layers
-   If one layer changes, everything after it rebuilds
-   Base image layers are shared across images

  -------------------
  WHY LAYERS MATTER
  -------------------

-   Faster rebuilds (layer caching)
-   Smaller image pushes (only changed layers upload)
-   Shared storage efficiency
-   Deterministic, reproducible environments

Docker images are layered filesystem snapshots.

  ---------------------
  FUTURE IMPROVEMENTS
  ---------------------

-   Multi-stage build implementation.(In different project as I progress)
-   Different base image like Slim
-   Non-root container user for security
-   .dockerignore optimization
-   Image size comparison experiments


------------------------------------------------------------------------

This project is not just about running a container. It is about
understanding how Docker builds, layers, and optimizes images.

