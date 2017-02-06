# VERSION:        0.1
# DESCRIPTION:    Create chromium container with its dependencies
# ORIGINAL AUTHOR:         Jessica Frazelle <jess@docker.com>
# ADDED MAC SPECIFIC UPDATES: Wayne Dectatur
# UPDATE BASED ON:
# https://github.com/docker/docker/tree/b248de7e332b6e67b08a8981f68060e6ae629ccf/contrib/desktop-integration/chromium
# UPDATED DOCKERFILE:
# https://github.com/fomightez/dockerfiles/new/master/mac_desktop/chrome
# RELATED DOCKER IMAGE:
# https://hub.docker.com/r/fomightez/chromium/
# COMMENTS:
#   This file describes how to build a Chromium container with all
#   dependencies installed. It uses native X11 unix socket.
#   Tested on Debian Jessie
#   On a Mac running OSX 10.9.5 with Docker Toolbox and socat and XQuartz
# EASIEST USAGE, USE PRE-BUILT IMAGE:
#   # Run ephemeral on Mac
#   docker run -e DISPLAY=192.168.99.1:0 -v $HOME/Downloads:/root/Downloads --name chrome fomightez/chromium
#
# # Build your own and run it:
#   # Download Chromium Dockerfile
#   wget http://raw.githubusercontent.com/fomightez/dockerfiles/master/mac_desktop/chrome/Dockerfile
#   # Build chromium image
#   docker build -t chromium .
#
#   # Run ephemeral on Mac
#   docker run -e DISPLAY=192.168.99.1:0 -v $HOME/Downloads:/root/Downloads --name chrome chromium

# Base docker image
FROM debian:jessie
MAINTAINER Jessica Frazelle <jess@docker.com>

# Install Chromium
RUN apt-get update && apt-get install -y \
    chromium \
    chromium-l10n \
    libcanberra-gtk-module \
    libexif-dev \
    --no-install-recommends

# Autorun chromium
CMD ["/usr/bin/chromium", "--no-sandbox", "--user-data-dir=/data"]
