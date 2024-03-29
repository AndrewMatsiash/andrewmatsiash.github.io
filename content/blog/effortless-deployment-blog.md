---
title: Effortless Deployment of Web Applications with Kamal
author: Yahor Bukhta
avatar:
date: 2023-12-06T17:18:00
SEO:
  description: 'Learn how Kamal simplifies web app deployment with features like zero downtime, incremental rollouts, and flexible configuration. Ideal for developers.'
  keywords: 'Kamal, Web App Deployment, Zero Downtime, Incremental Rollouts, Flexible Configuration, Developers'
  author: 'Yahor Bukhta'
image: './images/kamal.png'
blog_categories: ['DevOps']
---

### Introduction:

Navigating the intricacies of web application deployment can be a daunting task. Enter Kamal—a deployment tool designed to alleviate these complexities. In this article, we'll delve into the key features of Kamal and share insights from our own hands-on experience.

### Key Advantages of Kamal

Kamal brings a host of features to the table, making it a go-to solution for web application deployment:

- **Zero Downtime**: Kamal ensures uninterrupted service during the deployment process.
- **Incremental Rollouts**: Update your application seamlessly without affecting the end-user experience.
- **Flexible Configuration**: Kamal provides extensive options for managing both environment variables and sensitive data securely.

### Why We Chose Kamal

In our development studio, Kamal has become an invaluable asset. Its intuitive configuration interface and comprehensive feature set have significantly reduced our deployment overhead, allowing us to channel our energies into development. The tool's robust handling of environment variables and secrets is especially noteworthy.

### Installation Guide

Installing Kamal is straightforward. Here are your options:

1. To add the gem to your Ruby on Rails project, simply execute **`bundle add kamal`**. Note that compatibility issues may arise with older Ruby on Rails versions.

2. For Docker users, you can create an alias in your console configuration to run Kamal as follows:

```bash
alias kamal="docker run -it --rm -v '${PWD}:/workdir' -v '/run/host-services/ssh-auth.sock:/run/host-services/ssh-auth.sock' -e SSH_AUTH_SOCK='/run/host-services/ssh-auth.sock' -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/basecamp/kamal:latest"
```

3. To avoid version conflicts, you can set up a separate Gemfile as follows:

- Create a file named **`gemfiles/kamal.Gemfile`** and populate it with:

```ruby
source 'https://rubygems.org'
gem 'kamal', '~> 1.0.0'
```

- Run

```bash
BUNDLE_GEMFILE=kamal/Gemfile bundle install
```

- Generate a runnable directory using

```bash
BUNDLE_GEMFILE=kamal/Gemfile bundle binstub kamal --path ../bin
```

After these steps, you can run **`bin/kamal`** from the console. This is particularly useful for GitHub Actions if adding the gem to your project isn't an option.

### Configuration Steps

Configuring Kamal is a breeze. Simply run **`kamal init`** to generate the necessary directories and files. You can then proceed to configure your deployment settings using the **`deploy.yml`** and **`deploy.<env name>.yml`** files.

Here's a sample **`deploy.yml`** file to get you started:

```yaml
# Name of your application. Used to uniquely configure containers.
service: <project_name>

# Name of the container image.
image: <docker_image_name>

# Deploy to these servers.
# servers:
#  - 192.168.0.1

# Credentials for your image host.
# registry:
# Specify the registry server, if you're not using Docker Hub
# server: registry.digitalocean.com / ghcr.io / ...

username:
  - DOCKER_REGISTRY_USERNAME # this is ENV variable

# Always use an access token rather than real password when possible.
password:
  - DOCKER_REGISTRY_PASSWORD # also ENV variable

# Inject ENV variables into containers (secrets come from .env).
# Remember to run `kamal env push` after making changes!
env:
#   clear:
#     DB_HOST: 192.168.0.2
secret:
  - RAILS_MASTER_KEY
  - MYSQL_ROOT_PASSWORD

# Use a different ssh user than root
# ssh:
#   user: app

# Configure builder setup.

builder:
  multiarch: false
  cache:
    type: registry
    options: mode=max,image-manifest=true,oci-mediatypes=true

# builder:
#   args:
#     RUBY_VERSION: 3.2.0
#   secrets:
#     - GITHUB_TOKEN
#   remote:
#     arch: amd64
#     host: ssh://app@192.168.0.1

# Use accessory services (secrets come from .env).
# accessories:
#   db:
#     image: mysql:8.1.0
#     host: 192.168.0.2
#     port: 3306
#     env:
#       clear:
#         MYSQL_ROOT_HOST: '%'
#       secret:
#         - MYSQL_ROOT_PASSWORD
#     directories:
#       - data:/var/lib/mysql
#   redis:
#     image: redis:7.2.1
#     host: 192.168.0.3
#     port: 6379
#     directories:
#       - data:/data

# Configure custom arguments for Traefik
# traefik:
#   args:
#     accesslog: true
#     accesslog.format: json

# Configure a custom healthcheck (default is /up on port 3000)
healthcheck:
  max_attempts: 30
# Bridge fingerprinted assets, like JS and CSS, between versions to avoid
# hitting 404 on in-flight requests. Combines all files from new and old
# version inside the asset_path.
# asset_path: /rails/public/assets

# Configure rolling deploys by setting a wait time between batches of restarts.
# boot:
#   limit: 10 # Can also specify as a percentage of total hosts, such as "25%"
#   wait: 2
```

And an example **`deploy.staging.yml`** for staging environments:

```yaml
servers:
  - <staging server ip>

env:
  clear:
    RAILS_ENV: staging
    DB_HOST: <staging DB server ip>
    REDIS_HOST: <staging Redis server ip>

accessories:
  db:
    image: mysql:8.1.0
    host: <staging DB server ip>
    port: 3306
    env:
      clear:
        MYSQL_ROOT_HOST: '%'
      secret:
        - MYSQL_ROOT_PASSWORD
    directories:
      - data:/var/lib/mysql
  redis:
    image: redis:7.2.1
    host: <staging Redis server ip>
    port: 6379
    directories:
      - data:/data
```

### Usage Guide

To set up your server, execute the command **`kamal setup`**. In our specific case, the command is:

```bash
kamal setup -d staging
```

To create a .env file, execute the command **kamal envify**. In our specific case, the command is:

```bash
kamal envify -d staging
```

To upload variables from .env files to the server, execute the command **kamal env push**. In our specific case, the command is:

```bash
kamal env push -d staging
```

To **deploy** your application, use the command **`kamal deploy`**. For us, the command is:

```bash
kamal deploy -d staging
```

### Conclusion

Kamal stands out as a streamlined and efficient solution for web application deployment. Its zero-downtime, incremental rollouts, and flexible configuration options make it an essential tool for any development team.
