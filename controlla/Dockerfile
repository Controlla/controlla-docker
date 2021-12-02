FROM cimg/php:7.4.26-node as builder

LABEL maintainer="Ivan Sotelo <isotelo@controlla.com.mx>"

RUN npm install -g @controlla/cli
COPY . /app

FROM baseImage

RUN --from=builder