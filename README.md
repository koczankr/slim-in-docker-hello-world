# slim-in-docker-hello-world

[![English](https://img.shields.io/badge/lang-English-blue)](README.md)
[![Magyar](https://img.shields.io/badge/lang-Magyar-red)](README.hu.md)

This is the English version of the README. For other languages:

- [Magyar verzió (Hungarian)](README.hu.md)

## Objective

**I want to create a Slim framework project in a Docker container environment. I want to store all of this in a GitHub repository.**

### 1. Docker container environment

It provides a portable and easily configurable development environment. Docker is a platform that allows you to run applications and their related components in containers. Containers provide an isolated, portable environment, and various applications, such as the Slim framework, are easy to manage.

- **Download and Install:** [Docker Desktop](https://www.docker.com/products/docker-desktop)
- **Documentation and guides:** [Docker Documentation](https://docs.docker.com/)

### 2. Slim Framework project

It is an ideal choice if you want to quickly develop a modern web or API-based application. Slim is a lightweight, PHP-based micro-framework for developing fast and efficient web applications.

- **Official site:** [Slim Framework](https://www.slimframework.com/)
- **Documentation:** [Slim Documentation](https://www.slimframework.com/docs/v4)

### 3. Storing it in a GitHub repository

GitHub is a cloud-based version control platform that allows you to store, version, and share your code with others. The repository (repo) is the place where all the files of the project and its history are stored.

- **GitHub registration and access:** [GitHub](https://github.com/)
- **GitHub Desktop:** [GitHub Desktop](https://desktop.github.com/)

---

## Level

**Absolute beginner**

---

## 1. Create a GitHub repository, clone it with GitHub Desktop and open the project in Visual Studio Code

This step shows you how to create a GitHub repo, clone it to your machine using GitHub Desktop, and open it in Visual Studio Code for development.

### 1.1 Create a public repo on GitHub

1. Go to [GitHub](https://github.com/) or register an account if you don't have one.
2. In the upper right corners, click on the "+" icon**, then select the **New repository** options.
3. Fill in the following fields:
 - **Repository name:** Enter the name of the repo, for example `slim-in-docker-hello-world`.
 - **Public:** Choose the public option (if you want to make it available to others).
4. Click a **Create Repository** field.

### 1.2 Clone with GitHub Desktop

1. Download and install [GitHub Desktop](https://desktop.github.com/).
2. Launch the app and sign in with your GitHub account.
3. In GitHub Desktop, choose the **File > Clone Repository** menu or click the **Clone Repository** option.
4. Select the previously original repo from the list, or enter its URL (several times: `https://github.com/felhaszalonev/slim-in-docker-hello-world.git`).
5. Select the local folder where you want to clone the repo, then click the **Clone** button.

### 1.3 Open it with Visual Studio Code in the folder

1. Download and install [Visual Studio Code](https://code.visualstudio.com/).
2. Start Visual Studio Code.
3. Click **File > Open Folder** and select the folder where you cloned the repo (otherwise: `~/Documents/slim-in-docker-hello-world`).
4. Your project will now open and you are ready to develop.

---

## 2. Prepare the container using docker-compose.yml and Dockerfile

### 2.1 docker-compose.yml

Create a `docker-compose.yml` file in the root of the project and have this content:

``` yaml
services:
 application:
 image: php:8.2-apache
 storage_name: slim-app
 ports:
 - "8080:80"
 volumes:
 - ./app:/var/www/html
 working directory: /var/www/html
 build:
 context: .
 dockerfile: Dockerfile
```

#### Explanation

- **`services`**: Defines a group of services. Here, the service called `app` is our PHP application.
- **`image: php:8.2-apache`**: The container uses PHP version 8.2 and the Apache web server.
- **`container_name: slim-app`**: Defines the name of the container.
- **`ports: "8080:80"`**: Connects port 8080 on the machine to port 80 of the container where Apache is running.
- **`volumes: ./app:/var/www/html`**: Attaches the contents of the local `./app` folder to the container's `/var/www/html` folder.
- **`working_dir: /var/www/html`**: Sets the default working directory inside the container.
- **`build`**: Specifies to build the container image locally based on the appropriate Dockerfile.

### 2.2 Dockerfile

Create a `Dockerfile` file in the root of the project with this content:

``` dockerfile
from php:8.2-apache

RUN apt-get update && apt-get install -y \
 unpack it \
 git \
 libzip-dev && \
 docker-php-ext-install zip

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
```

#### Explanation

#### Explanation

- **`FROM php:8.2-apache`**: The base image that contains PHP 8.2 and the Apache web server.
- **`RUN apt-get update && apt-get install -y ...`**: Installs the necessary packages:
 - **`unzip`**: To unzip files.
 - **`git`**: For version control (for example Composer dependencies).
 - **`libzip-dev`**: Required for ZIP support.
 - **`docker-php-ext-install zip`**: Activate the PHP ZIP extension.
- **`COPY --from=composer:latest /usr/bin/composer /usr/bin/composer`**: Copies the latest Composer version from another Docker image.

---

## 3. Start the container and install the Slim framework

### 3.1 Starting a container

```bash
docker-compose build
docker-compose up -d
```

- **`docker-compose build`**: Builds the image based on the Dockerfile.
- **`docker-compose up -d`**: Starts the container in the background.

### 3.2 Installing Slim Skeleton

```bash
docker exec -it slim-app bash
composer create-project slim/slim-skeleton .
```

- **`docker exec -it slim-app bash`**: You enter the container.
- **`composer create-project slim/slim-skeleton .`**: Composer downloads and initializes the Slim Skeleton project in the current directory.

### 3.3 Enable Apache mod_rewrite

```bash
a2enmod rewrite
service apache2 restart
```

- **`a2enmod rewrite`**: Enables

 Apache's mod_rewrite module.
- **`service apache2 restart`**: Restarts Apache.

If you receive the following message:
**`Restarting Apache httpd web server: apache2Terminated'**,
means that Apache was not restarted properly inside the container.

#### Alternative solution: automatically enable rewrite module in Dockerfile

Instead of manually running `a2enmod rewrite` every time you rebuild, you can automate this step in `Dockerfile`. Add the following line to the end of `Dockerfile`:

```dockerfile
RUN a2enmod rewrite
```

This will automatically activate Apache's `mod_rewrite` module every time the container is started.

---

### 4. Done! Hello World!

If everything is set up correctly, now when you open **`http://localhost:8080`** in your browser, you should see the Slim Skeleton framework welcome page displaying "Hello World!" text.

#### Further development

You can work in the `./app` folder, which is synchronized with the `/var/www/html` folder of the container. This means that file changes will appear in the browser immediately after an update.

---

### 5. About docker-compose commands

#### 5.1 `docker-compose down/up` = Reinstall

This is similar to reinstalling a native environment.

- **`down`:**
 - Removes all temporary resources (containers, networks).
 - Volumes (data) are deleted only if you explicitly specify the `--volumes` switch.
 - On restart (`up`), completely new containers are created based on `docker-compose.yml`.

- **`up`:**
 - Creates new containers as if you were just starting the project.
 - Installs required dependencies (such as Composer or NPM).
 - Everything starts fresh and clean.

#### 5.2 `docker-compose stop/start` = Hibernate

This works like a computer's sleep mode.

- **`stop`:**
 - Stops containers but preserves their current state (such as files and configurations).
 - The network and container configuration are also preserved.
 - After restarting, you can continue where you left off.

- **`start`:**
 - Simply restarts stopped containers with their current state.
 - Fast because it doesn't rebuild the environment.

---

### 5.3 When to use which one?

| **Target** | **Command to use** |
|------------------------------------------------ --|------------------------------------------------ --------|
| Quickly resume development where you left off | `docker-compose stop` → `docker-compose start` |
| Eliminate a faulty or outdated environment | `docker-compose down` → `docker-compose up` |
| Cleanly rebuild the environment | `docker-compose down` → `docker-compose up --build` |
| Stop containers only temporarily | `docker-compose stop` |

---

### 5.4 Important to know

1. **Volumes (data storage):**
 - If the container uses volumes (e.g. databases), they remain even after `down`, unless you explicitly delete them with the `--volumes` switch.
 - For a full reinstall you can use:
 ```bash
 docker-compose down --volumes
 ```

2. **Rebuilding (`--build` switch):**
 - If you change something in the `Dockerfile`, always use: to rebuild the environment:
 ```bash
 docker-compose up --build
 ```

3. **Data and conditions:**
 - `stop/start` preserves the internal state and configuration of containers.
 - `down/up' resets everything except the volume data, unless you delete them.

---

### 5.5 Briefly

- **`stop/start` = Hibernation:** Fast because nothing is rebuilt.
- **`down/up` = Reinstall:** Clean environment, but all containers and networks are recreated.

---

### 6. Summary

With this documentation, you can set up a complete development environment and effectively use the advantages provided by Docker and Slim Framework! Happy Coding!