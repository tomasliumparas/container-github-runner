FROM debian:buster

ARG GH_RUNNER_VERSION="2.160.0"
ARG DOCKER_COMPOSE_VERSION="1.24.1"

ENV RUNNER_NAME=""
ENV RUNNER_WORK_DIRECTORY="_work"
ENV RUNNER_TOKEN=""
ENV RUNNER_REPOSITORY_URL=""


ENV SUMMARY="Dockerized GitHub Actions runner."
ENV DESCRIPTION="Dockerized GitHub Actions runner. $GH_RUNNER_VERSION"

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.display-name="Github runner" \
      io.openshift.tags="github-runner" \
      name="tomasliumparas/github-runner" \
      version="0.1" \
      usage="docker run -it tcardonne/github-runner:latest" \
      maintainer="Tomas Liumparas <tomas.liumparas@gmail.com>"


RUN DEBIAN_FRONTEND=noninteractive apt-get update && \
    apt-get install -y \
        curl \
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common \
        git \
        sudo

# Install Docker CLI
RUN curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh

# Install Docker-Compose
RUN curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
    chmod +x /usr/local/bin/docker-compose

RUN rm -rf /var/lib/apt/lists/* && \
    apt-get clean

RUN useradd -ms /bin/bash runner && \
    usermod -aG docker runner && \
    usermod -aG sudo runner && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

WORKDIR /home/runner

RUN curl -O https://githubassets.azureedge.net/runners/${GH_RUNNER_VERSION}/actions-runner-linux-x64-${GH_RUNNER_VERSION}.tar.gz \
    && tar -zxf actions-runner-linux-x64-${GH_RUNNER_VERSION}.tar.gz \
    && rm -f actions-runner-linux-x64-${GH_RUNNER_VERSION}.tar.gz \
    && ./bin/installdependencies.sh \
    && chown -R runner: /home/runner

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["./run.sh"]

USER runner
