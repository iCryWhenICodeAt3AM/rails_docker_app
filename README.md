# README

This README would normally document whatever steps are necessary to get the
application up and running.

Things you may want to cover:

* Ruby version

* System dependencies

* Configuration

* Database creation

* Database initialization

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions

* ...


------ DEV NOTES ------
I used Ubuntu WSL for this with VSCode for the terminal and code edit, currently in main is the working version. Faced multiple issues with the tutorial, here's some of the fix attempts i did (the last part resolved the issue), don't mind the structure, its a bit messy:

# Ubuntu Docker Install and Setup

## Install Docker
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

## Start Docker
```bash
sudo dockerd
```

## Add User to Docker Group
```bash
sudo usermod -aG docker $USER
exec su -l $USER
```

## Locate Your Folder
```bash
cd {foldername}
```

## Start Docker Again
```bash
sudo dockerd
```

## Build the Docker
```bash
docker build .
```

---

## Troubleshooting Docker Build Issues

### Manually Install Docker Buildx
```bash
curl -LO https://github.com/docker/buildx/releases/download/v0.10.0/buildx-v0.10.0.linux-amd64
sudo mv buildx-v0.10.0.linux-amd64 /usr/local/bin/docker-buildx
sudo chmod +x /usr/local/bin/docker-buildx
```

### Verify PATH for Buildx
```bash
echo $PATH
export PATH=$PATH:/usr/local/bin
docker buildx version
```

### Verify Buildx Binary
```bash
/usr/local/bin/docker-buildx version
```

---

## Resolving Persistent Issues

### Adding User to Docker Group
```bash
sudo usermod -aG docker $USER
exec su -l $USER
```

### Check iptables
```bash
sudo update-alternatives --get-selections | grep iptables
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
```

### Stop and Kill Docker Instances
```bash
sudo service docker stop
sudo killall -9 dockerd
```

---

## Restart Docker
```bash
sudo dockerd
```

## Verify Docker is Working
```bash
docker ps
```

## Build Your Docker App
```bash
cd {path-to-your-app}
sudo chmod 666 /var/run/docker.sock
```

---

## Dockerfile Example

```dockerfile
FROM ruby:3.4.3-slim

# Install rails dependencies
RUN apt-get update -qq && \
    apt-get install --no-install-recommends -y build-essential libpq-dev nodejs libsqlite3-dev curl libvips libyaml-dev && \
    rm -rf /var/lib/apt/lists /var/cache/apt/archives

# Create and set working directory
WORKDIR /app

# Copy Gemfile and install dependencies
COPY Gemfile Gemfile.lock ./
RUN bundle install

# Copy the rest of the application
COPY . .

# Add a script to be executed every time the container starts
COPY bin/docker-entrypoint /usr/bin/
RUN chmod +x /usr/bin/docker-entrypoint
ENTRYPOINT ["docker-entrypoint"]

# Configure the main process to run when running the image
EXPOSE 3000
CMD ["rails", "server", "-b", "0.0.0.0"]
```

---

## Build and Run Docker App

### Build Docker Image
```bash
sudo docker build -t rails_docker_app .
```

### Show Docker Images
```bash
docker images
```

### Tag Docker Image
```bash
docker tag <image_id> rails-docker-app:v0.0.1
```
Example:
```bash
docker tag f5bdcfce4e6e rails-docker-app:v0.0.1
```

### Verify Tagged Image
```bash
docker images
```

### Run Docker Image
```bash
docker run -it -p 3000:3000 rails-docker-app:v0.0.1 bundle exec rails server -b 0.0.0.0 -p 3000
```
Alternative:
```bash
docker run -it -p 3000:3000 <REPOSITORY_NAME>:<IMAGE_TAG> bundle exec rails server -b 0.0.0.0 -p 3000
```
