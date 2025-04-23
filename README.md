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


# DEV NOTES

I used **Ubuntu WSL** for this setup with **VSCode** for the terminal and code editing.

## Key Notes:
- **Main branch contains the working version**.
- Faced **multiple issues** while following the tutorial.
- The fixes I attempted are listed below, along with troubleshooting steps and resolutions.
- The **last section resolved the issue**, and everything works as expected now.

---

## Full Setup Process

### 1. Install Docker
1. Update the system and install Docker:
   ```bash
   sudo apt update
   sudo apt install docker.io -y
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

2. Start the Docker daemon:
   ```bash
   sudo dockerd
   ```

3. Add your user to the Docker group to avoid using `sudo` frequently:
   ```bash
   sudo usermod -aG docker $USER
   exec su -l $USER
   ```

4. Navigate to your project folder:
   ```bash
   cd {foldername}
   ```

5. Restart Docker:
   ```bash
   sudo dockerd
   ```

6. Build the Docker image:
   ```bash
   docker build .
   ```

### 2. Example Dockerfile
Below is the Dockerfile used for the Rails application setup:

```dockerfile
FROM ruby:3.4.3-slim

# Install Rails dependencies
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

## Troubleshooting and Fix Attempts

### Issue 1: Docker Permissions
- **Problem**: Encountered permission issues when running Docker commands.
- **Solution**: Add the user to the Docker group to resolve permissions:
  ```bash
  sudo usermod -aG docker $USER
  exec su -l $USER
  ```

### Issue 2: iptables Conflict
- **Problem**: Docker failed to start due to iptables conflicts.
- **Solution**: Switch to Docker legacy iptables:
  ```bash
  sudo update-alternatives --get-selections | grep iptables
  sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
  ```

### Issue 3: Docker Daemon Failing to Start
- **Problem**: Docker daemon (`dockerd`) wouldn’t start after stopping Docker.
- **Solution**:
  1. Stop all Docker instances:
     ```bash
     sudo service docker stop
     sudo killall -9 dockerd
     ```
  2. Restart Docker daemon:
     ```bash
     sudo dockerd
     ```

### Issue 4: Buildx Not Found
- **Problem**: Docker Buildx was missing during the build process.
- **Solution**:
  1. Manually install Buildx:
     ```bash
     curl -LO https://github.com/docker/buildx/releases/download/v0.10.0/buildx-v0.10.0.linux-amd64
     sudo mv buildx-v0.10.0.linux-amd64 /usr/local/bin/docker-buildx
     sudo chmod +x /usr/local/bin/docker-buildx
     ```
  2. Verify Buildx installation:
     ```bash
     docker buildx version
     /usr/local/bin/docker-buildx version
     ```

### Issue 5: Docker Socket Permission
- **Problem**: Encountered permission issues with Docker socket.
- **Solution**: Allow access to the Docker socket:
  ```bash
  sudo chmod 666 /var/run/docker.sock
  ```

---

## RESOLVED THE ISSUE (Final Fixes)

### Key Steps to Resolution:
1. **Add User to Docker Group**:
   ```bash
   sudo usermod -aG docker $USER
   exec su -l $USER
   ```

2. **Fix iptables Conflict**:
   ```bash
   sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
   ```

3. **Restart Docker Daemon**:
   ```bash
   sudo dockerd
   ```

4. **Verify Docker Functionality**:
   ```bash
   docker ps
   ```

5. **Handle Permissions for Docker Socket**:
   ```bash
   sudo chmod 666 /var/run/docker.sock
   ```

---

## Final Steps: Building and Running the App

1. **Build the Docker Image**:
   ```bash
   sudo docker build -t rails_docker_app .
   ```

2. **Tag the Docker Image**:
   - View Docker images:
     ```bash
     docker images
     ```
   - Tag the image:
     ```bash
     docker tag <image_id> rails-docker-app:v0.0.1
     ```
     Example:
     ```bash
     docker tag f5bdcfce4e6e rails-docker-app:v0.0.1
     ```

3. **Run the Docker Container**:
   ```bash
   docker run -it -p 3000:3000 rails-docker-app:v0.0.1 bundle exec rails server -b 0.0.0.0 -p 3000
   ```

   - Alternatively:
     ```bash
     docker run -it -p 3000:3000 <REPOSITORY_NAME>:<IMAGE_TAG> bundle exec rails server -b 0.0.0.0 -p 3000
     ```

---

### Final Outcome:
After applying the above fixes, the Docker setup works perfectly. The Rails app runs successfully, and all permission and setup issues have been resolved.
