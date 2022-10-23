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
