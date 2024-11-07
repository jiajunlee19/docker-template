# Introduction

This examples shows how to use Docker on a react-next-app with some common docker commands listed.

<br>

## Running Locally

First, run the development server:

```bash
npm run dev
# or
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

<br>

## Containerize with Docker
1. Configure `standalone` output in [next.config.js](/next.config.js)
    ```js
    const nextConfig = {
        output: 'standalone',
    }
    ```
2. [Dockerfile](/Dockerfile) is created to build docker image. 
    - It uses multi-stage concept (base -> deps -> builder -> runner) and pass only relevant files between stages.
    - Production image generated from runner stage, contains only the necessary files needed to run the app.
    - Instead of the `root` user, the production image will be run as a normal privileged `nextjs` user.

3. [compose.yaml](/compose.yaml) is created to easily manage docker run process (including network creation, container creation etc.)
    - Use `docker compose --env-file .env up -d` to startup the docker container.
    - Use `docker compose down` to shutdown the docker container.

<br>

### Docker Commands
- Build a docker image, named as `docker-template`
  ```
  docker build -t docker-template .
  ```

- Run a docker container, published on `HOST:CONTAINER` port
  ```
  docker run -d -p 0.0.0.0:3000:3000 docker-template
  ```

- Tag and push a docker image
  ```
  docker tag docker-template:latest jiajunlee19/docker-template:latest
  docker push jiajunlee19/docker-template:latest
  ```

- Create a docker volume
  ```
  docker volume create docker-template-db
  ```

- Mount container to a docker volume
  ```
  docker run -d -p 0.0.0.0:3000:3000 --mount type=volume,src=docker-template-db,target=/etc/todos docker-template
  ```

- Create a docker network
  ```
  docker network create docker-template
  ```

- Running docker with [compose.yaml](/compose.yaml)
  ```
  docker compose up -d
  ```