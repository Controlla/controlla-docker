FROM golang:1.21.1

RUN apt-get update && apt-get install -y --no-install-recommends unzip && \
  wget https://github.com/opentofu/opentofu/releases/download/v1.6.2/tofu_1.6.2_linux_amd64.zip -q && \
  unzip tofu_1.6.2_linux_amd64.zip && \
  rm tofu_1.6.2_linux_amd64.zip && \
  mv tofu /usr/local/bin/tofu && \
  wget "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -q && \
  unzip awscli-exe-linux-aarch64.zip && \
  rm awscli-exe-linux-aarch64.zip && \
  ./aws/install