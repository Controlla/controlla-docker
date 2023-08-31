FROM golang:1.16.6

RUN apt-get update
RUN apt-get install unzip
RUN wget https://releases.hashicorp.com/terraform/1.5.6/terraform_1.5.6_linux_amd64.zip
RUN unzip terraform_1.5.6_linux_amd64.zip
RUN rm terraform_1.5.6_linux_amd64.zip
RUN mv terraform /usr/local/go/bin/terraform


RUN apt-get install git bash openssl

RUN go get github.com/gruntwork-io/terratest/modules/terraform
RUN go get -u github.com/golang/dep/cmd/dep

WORKDIR $GOPATH/src/github.com/gruntwork-io/terratest/modules/terraform

RUN go install
WORKDIR $GOPATH/src/app/test/
ENTRYPOINT ["sh", "-c", "dep ensure -v && go test -v"]