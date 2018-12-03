# Cactus Backend Admin
[![Build Status](https://travis-ci.org/Krystian19/cactus-app-backend-admin-service.svg?branch=master)](https://travis-ci.org/Krystian19/cactus-app-backend-admin-service) [![Code Climate](https://codeclimate.com/github/Krystian19/cactus-app-backend-admin-service/badges/gpa.svg)](https://codeclimate.com/github/Krystian19/cactus-app-backend-admin-service) [![Test Coverage](https://codecov.io/gh/Krystian19/cactus-app-backend-admin-service/branch/master/graph/badge.svg)](https://codecov.io/gh/Krystian19/cactus-app-backend-admin-service)


## Requirements
```sh
docker -v
Docker version 18.03.0-ce # or later
```

# Setup
docker-compose file example:
```yaml
version: '2.3'

services:
  mysql:
    image: mysql:5.7.24
    environment:
      MYSQL_ROOT_PASSWORD: "secret"
      MYSQL_DATABASE: "cactus_app"
    healthcheck:
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
      timeout: 20s
      retries: 10
    ports: ['3306:3306']

  phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    environment:
      MYSQL_ROOT_PASSWORD: "1234"
      PMA_HOST: "mysql"
      PMA_PORT: "3306"
    ports: ['8080:80']
    links:
      - mysql

  backend_admin:
    build:
      ./cactus-app-backend-admin-service
    volumes:
      - ./cactus-app-backend-admin-service:/code
    environment:
      ENV: "development"
      DJANGO_ADMIN_USER: admin
      DJANGO_ADMIN_PASSWORD: "pass1234"
    healthcheck:
      test: ["CMD",  "curl", "-I", "--fail", "http://localhost:8000/admin"]
      timeout: 20s
      retries: 10
    ports: ['6500:8000']
    links:
      - mysql
    depends_on:
      mysql:
        condition: service_started
      mysql:
        condition: service_healthy
```

## Run the tests
Once the service is up and assuming the service container is called "cactus_backend", then run ...
```sh
docker exec -ti backend_admin python /code/manage.py test
```

## Update seeder data
Update the initial data file with this command, once you execute it, make the commit:
```sh
docker exec -ti backend_admin_1 /code/load_data.sh
```

## License
MIT © [Jan Guzman](https://github.com/Krystian19)
