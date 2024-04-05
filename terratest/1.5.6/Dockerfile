FROM golang:1.16.6

RUN apt-get install -y --no-install-recommends unzip=6.0 && \
  curl -q https://releases.hashicorp.com/terraform/1.5.6/terraform_1.5.6_linux_amd64.zip -O && \
  unzip terraform_1.5.6_linux_amd64.zip && \
  rm terraform_1.5.6_linux_amd64.zip && \
  mv terraform /usr/local/go/bin/terraform && \
  curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2.zip" && \
  unzip awscliv2.zip && \
  rm awscliv2.zip && \
  ./aws/install
