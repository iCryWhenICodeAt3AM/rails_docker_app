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
- I faced **multiple issues** while following the tutorial. 
- The fixes I attempted are listed below (the last section resolved the issue).

---

## Fix Attempts (Messy Structure)

### Issue: Docker Permissions
- Adding the user to the Docker group resolved most permission issues:
  ```bash
  sudo usermod -aG docker $USER
  exec su -l $USER
  ```

### Issue: iptables Conflict
- Switched to Docker legacy iptables:
  ```bash
  sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
  ```

### Issue: Docker Daemon Failing to Start
- Stopped all Docker instances:
  ```bash
  sudo service docker stop
  sudo killall -9 dockerd
  ```

- Restarted the Docker daemon:
  ```bash
  sudo dockerd
  ```

### Issue: Buildx Not Found
- Manually installed Docker Buildx:
  ```bash
  curl -LO https://github.com/docker/buildx/releases/download/v0.10.0/buildx-v0.10.0.linux-amd64
  sudo mv buildx-v0.10.0.linux-amd64 /usr/local/bin/docker-buildx
  sudo chmod +x /usr/local/bin/docker-buildx
  ```

- Verified Buildx installation:
  ```bash
  docker buildx version
  /usr/local/bin/docker-buildx version
  ```

### Issue: Docker Socket Permission
- Allowed access to the Docker socket:
  ```bash
  sudo chmod 666 /var/run/docker.sock
  ```

---

## RESOLVED THE ISSUE (Final Fixes)

### Key Steps:
1. **Add User to Docker Group**:
   ```bash
   sudo usermod -aG docker $USER
   exec su -l $USER
   ```

2. **Fix iptables**:
   ```bash
   sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
   ```

3. **Restart Docker**:
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

### Final Outcome:
The Docker setup works perfectly with the above fixes applied. Everything is functioning as expected after resolving the permission and configuration issues.
