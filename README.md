# herokuDocker-labAlexis

Partimos de un archivo Dockerfile con la siguiente configuración:

    - - - - - 
        FROM node:16-alpine AS base
        RUN mkdir -p /usr/app
        WORKDIR /usr/app

        # Prepare static files
        FROM base AS build-front
        COPY ./ ./
        RUN npm ci
        RUN npm run build

        # Release
        FROM base AS release
        ENV STATIC_FILES_PATH=./public
        COPY --from=build-front /usr/app/dist $STATIC_FILES_PATH
        COPY ./server/package.json ./
        COPY ./server/package-lock.json ./
        COPY ./server/index.js ./
        RUN npm ci --only=production

        ENV PORT=8080
        ENTRYPOINT [ "node", "index" ]
    - - - - - 

Creamos repositorio
Git init
Git remote add https://github.com/AlexisTFY/herokuDocker-labAlexis.git
git add .
git commit -m "subir archivos base"
git push -u origin main

Crear arichivo cd.yml en .github/workflows

    - - - - - 
        name: Continuos Deployment workflow

        on: pull_request

        jobs:
        ci:
            runs-on: ubuntu-latest
            steps:
            - name: Checkout repository
                uses: actions/checkout@v3
            - name: Install
                run: npm ci
            - name: Build
                run: npm run build
            - name: Test
                run: npm test
        
    - - - - - 
Creamos nueva rama, hacemos push y hacemos una pull request

Una vez que esta correcta esta parte vamos a heroky y creamos una nueva app
name: heroku-docker-labalexis
zone: Europa

Creamos token de autorización
$ npx heroku login
$ npx heroku authorizations:create -d heroku-docker-labalexis
Token:       f6fac01a-0ea2-4a1c-8270-42a52e8e75b1
Añadimos ese token en Gihub en la zona de Secrets
Creamos otros secreto con el nombre de la app de heroku