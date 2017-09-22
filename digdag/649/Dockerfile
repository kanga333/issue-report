FROM ubuntu:16.04

RUN apt-get update && apt-get install -y \
  curl openjdk-8-jdk\
  && rm -rf /var/lib/apt/lists/*

#Install the docker client to launch the sibling container inside the container.
ENV DOCKER_CLIENT_VERSION=1.12.6
ENV DOCKER_API_VERSION=1.24
RUN curl -fsSL https://get.docker.com/builds/Linux/x86_64/docker-${DOCKER_CLIENT_VERSION}.tgz \
  | tar -xzC /usr/local/bin --strip=1 docker/docker

#Install digdag with the same content as Getting started.
RUN curl -o ~/bin/digdag --create-dirs -L "https://dl.digdag.io/digdag-latest" \
    && chmod +x ~/bin/digdag \
    && echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc \
    && mkdir -p digdag/sessions

EXPOSE 65432

CMD /root/bin/digdag server --memory -b 0.0.0.0 -O digdag/sessions