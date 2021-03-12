## 2. プロジェクト  クローン
まずこのハンズオンでオマージュするプロジェクト [https://github.com/osonoi/node-build-config-openshift](https://github.com/osonoi/node-build-config-openshift)をクローンします。<br>

```
git clone https://github.com/IBM/node-build-config-openshift
cd node-build-config-openshift
```

今クローンしたディレクトリにある**Dockerfile**を見れば、これから作成しようとしているアプリがどのようにコンテナライズされているのか分かります。<br>

```
cat Dockerfile
```

**Dockerfile**内の中身です。

```
# Use the official Node 10 image
FROM node:10

# Change directory to /usr/src/app
WORKDIR /usr/src/app

# Copy the application source code
COPY . .

# Change directory to site/
WORKDIR site/

# Install dependencies
RUN npm install

# Allow traffic on port 8080
EXPOSE 8080

# Start the application
CMD [ "npm", "start" ]
```



