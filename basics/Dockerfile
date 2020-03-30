# Demonstrates variable in FROM directive, allowing us to parameterize the base image
# Variables here are "global" and can be utilized by any stage
ARG BASE_IMAGE_URI=ubuntu
ARG TAG=18.04

# === Base image. We can use FROM and use the same of the stage to "inherit" the base image===
FROM ${BASE_IMAGE_URI}:${TAG} as base

RUN apt-get update -qq && apt-get install -yq \
        curl \
    && rm -rf /var/lib/apt/lists/*

# === Use busybox (1.2 MB!) as a base image if you want to just load some data ===
FROM busybox as our_data_image

COPY data /data

# === Emulate copying in some chunk of data from another stage ===
FROM base as base_with_data

# Use the COPY --from=IMAGE directive to copy directly from another image. Can be local or remote (will auto pull).
# This is useful for "shrinking" build images. You can stage your build with earlier stages downloading all of the
# build-time dependencies, build your binary, and then copy than into the final image.
COPY --from=our_data_image /data /data

# If you want to deliberately trigger a cache-miss, you can ADD some bytes from random.org
# ADD downloads the file, checks its hash, and if it's different than the last time it downloaded the file,
# it'll cache-miss. This is why you should use ADD for URLs that have variable content, and RUN curl ... for
# endpoints with unchanging content.
#ADD "https://www.random.org/strings/?num=1&len=20&digits=on&unique=on&format=plain&rnd=new" /nonce.txt

RUN cat /data/cool_story.txt


