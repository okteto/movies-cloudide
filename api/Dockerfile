FROM bitnami/kubectl:1.17.4 as kubectl

FROM codercom/ubi-node:1.0.0

USER root

COPY --from=kubectl /opt/bitnami/kubectl/bin/kubectl /usr/local/bin/kubectl

WORKDIR /src

COPY package.json yarn.lock ./
RUN yarn install

COPY . .

CMD ["dumb-init", "code-server", "--auth", "none", "--port", "8081", "--host", "0.0.0.0", "/src"]