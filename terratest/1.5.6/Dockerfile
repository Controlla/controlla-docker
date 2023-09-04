FROM golang:1.16.6

RUN apt-get update
RUN apt-get install unzip
RUN wget https://releases.hashicorp.com/terraform/1.5.6/terraform_1.5.6_linux_amd64.zip
RUN unzip terraform_1.5.6_linux_amd64.zip
RUN rm terraform_1.5.6_linux_amd64.zip
RUN mv terraform /usr/local/go/bin/terraform
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN rm awscliv2.zip
RUN ./aws/install
