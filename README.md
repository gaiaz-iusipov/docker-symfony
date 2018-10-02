# docker-symfony

[![GitHub](https://img.shields.io/github/license/gaiaz-iusipov/docker-symfony.svg)](https://github.com/gaiaz-iusipov/docker-symfony#license)

:whale: A [Docker](https://www.docker.com/) multicontainer with Nginx, PHP 7.2, MySQL 5.7 for the [Symfony 4](https://symfony.com/4) web framework

## Features

- OPcache
- APCu
- [Xdebug](https://xdebug.org/)
- [fixuid](https://github.com/boxboat/fixuid)

## Installation

1. Clone this repository

    ```bash
    git clone https://github.com/gaiaz-iusipov/docker-symfony.git
    ```

2. Create a `.env` from the `.env.dist` file

    ```bash
    cd docker-symfony
    cp .env.dist .env
    ```

3. Adapt `.env` according to your symfony application

4. Build and run the containers

    ```bash
    docker-compose build
    docker-compose up -d
    ```

5. Install dependencies and create database schema

    ```bash
    docker-compose exec php
    composer install
    bin/console doctrine:schema:create
    ```

## Usage

1. Run the containers

    ```bash
    docker-compose up -d --build
    ```

2. Open [http://localhost](http://localhost) :tada:

## Configuring Xdebug in PhpStorm

1. In the Settings/Preferences dialog, select **Languages and Frameworks | PHP | Servers**.
2. Add a server:

    * Name: `docker`
    * Host: `localhost`
    * Absolute path on the server: `/app`

## License

[MIT](http://opensource.org/licenses/MIT) © [Gaiaz Iusipov](https://github.com/gaiaz-iusipov)
