# my-app

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Run your tests
```
npm run test
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

# Vue - Docker
---
- Create Vue Project
    ```zsh
    npm install -g @vue/cli@3.7.0
    vue create my-app --default
    cd my-app
    ```
- Dockerfile
    ```docker
    # base image
    FROM node:12.2.0-alpine

    # set working directory
    WORKDIR /app

    # add `/app/node_modules/.bin` to $PATH
    ENV PATH /app/node_modules/.bin:$PATH

    # install and cache app dependencies
    COPY package.json /app/package.json
    RUN npm install
    RUN npm install @vue/cli@3.7.0 -g

    # start app
    CMD ["npm", "run", "serve"]
    ```
- .dockerignore
    ```docker
    node_modules
    .git
    .gitignore
    ```
- Build and tag the Docker image:
    ```zsh
    docker build -t my-app:dev .
    ```
- Then, spin up the container once the build is done:
    ```zsh
    docker run -v ${PWD}:/app -v /app/node_modules -p 8081:8080 --rm my-app:dev
    ```


    1. The [docker run](https://docs.docker.com/engine/reference/commandline/run/) command creates a new container instance, from the image we just created, and runs it.

    2. `-v ${PWD}:/app` mounts the code into the container at “/app”.

        {PWD} may not work on Windows. See [this](https://stackoverflow.com/questions/41485217/mount-current-directory-as-a-volume-in-docker-on-windows-10) Stack Overflow question for more info.

    3. Since we want to use the container version of the “node_modules” folder, we configured another volume: `-v /app/node_modules`. You should now be able to remove the local “node_modules” flavor.

    4. `-p 8081:8080` exposes port 8080 to other Docker containers on the same network (for inter-container communication) and port 8081 to the host.

        For more, review [this](https://stackoverflow.com/questions/22111060/what-is-the-difference-between-expose-and-publish-in-docker) Stack Overflow question.

    5. Finally, --rm [removes](https://docs.docker.com/engine/reference/run/#clean-up---rm) the container and volumes after the container exits.

        Open your browser to http://localhost:8081 and you should see the app. Try making a change to the `App` component (src/App.vue) within your code editor. You should see the app hot-reload. Kill the server once done.
    ```zsh
    docker run -it -v ${PWD}:/app -v /app/node_modules -p 8081:8080 --rm my-app:dev
    ```
- docker-compose.yml
    ```docker
    version: '3.7'

    services:

    my-app:
        container_name: my-app
        build:
        context: .
        dockerfile: Dockerfile
        volumes:
        - '.:/app'
        - '/app/node_modules'
        ports:
        - '8081:8080'
    ```
- Build the image and fire up the container:
    ```zsh
    docker-compose up -d --build
    ```
- Ensure the app is running in the browser and test hot-reloading again. Bring down the container before moving on:
    ```zsh
    docker-compose stop
    ```
---