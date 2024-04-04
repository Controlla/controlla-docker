FROM golang:1.21.1

RUN apt-get update && apt-get install unzip && \
  wget https://github.com/opentofu/opentofu/releases/download/v1.6.2/tofu_1.6.2_linux_amd64.zip && \
  unzip tofu_1.6.2_linux_amd64.zip && \
  rm tofu_1.6.2_linux_amd64.zip && \
  mv tofu /usr/local/bin/tofu && \
  curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2.zip" && \
  unzip awscliv2.zip && \
  rm awscliv2.zip && \
  ./aws/install