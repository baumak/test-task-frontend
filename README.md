# test-task-front

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

#Docker 

##build image

docker build -t test-task/frontend .

##create container on port 8080
docker run -d -it -p 8080:80 --rm --name test-task-frontend test-task/frontend