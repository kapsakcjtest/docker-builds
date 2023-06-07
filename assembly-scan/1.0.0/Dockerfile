# FROM defines the base docker image. This command has to come first in the file
# The 'as' keyword lets you name the folowing stage. We use `app` for the production image
FROM ubuntu:jammy as app

# ARG sets environment variables during the build stage
ARG ASSEMBLY_SCAN_VER="1.0.0"

# LABEL instructions tag the image with metadata that might be important to the user
# Optional, but highly recommended
LABEL base.image="ubuntu:jammy"
LABEL dockerfile.version="1"
LABEL software="assembly-scan"
LABEL software.version="${ASSEMBLY_SCAN_VER}"
LABEL description="Generate basic stats for an assembly."
LABEL website="https://github.com/rpetit3/assembly-scan"
LABEL license="https://github.com/rpetit3/assembly-scan/blob/master/LICENSE"
LABEL maintainer="Curtis Kapsak"
LABEL maintainer.email="kapsakcj@gmail.com"

# RUN executes code during the build
# Install dependencies via apt-get or yum if using a centos or fedora base
RUN apt-get update && apt-get install -y \
 python3-minimal \
 wget \
 ca-certificates

# Install and/or setup more things. Make /data for use as a working dir
RUN wget https://github.com/rpetit3/assembly-scan/archive/refs/tags/v${ASSEMBLY_SCAN_VER}.tar.gz && \
 tar -xzf v${ASSEMBLY_SCAN_VER}.tar.gz && \
 rm -v v${ASSEMBLY_SCAN_VER}.tar.gz && \
 mv -v assembly-scan-${ASSEMBLY_SCAN_VER} /assembly-scan && \
 mkdir /data

# ENV instructions set environment variables that persist from the build into the resulting image
# Use for e.g. $PATH and locale settings for compatibility with Singularity
ENV PATH="${PATH}:/assembly-scan/bin" \
 LC_ALL=C

# WORKDIR sets working directory
WORKDIR /data

# A second FROM insruction creates a new stage
# We use `test` for the test image
FROM app as test

# Demonstrate that the program is successfully installed

# change working directory to where assembly-scan's test files are located
WORKDIR /assembly-scan/test

# print help and version information
RUN assembly-scan --help && assembly-scan --version

# run tests as specified in https://github.com/rpetit3/assembly-scan/blob/master/.github/workflows/assembly-scan.yml
RUN assembly-scan phiX174.fna && \
assembly-scan phiX174.fna --json && \
assembly-scan phiX174.fna --transpose && \
assembly-scan saureus.fasta.gz && \
assembly-scan saureus.fasta.gz --json && \
assembly-scan saureus.fasta.gz --transpose

