# Installing Redis
### Prerequisites
● Ubuntu system (e.g., 20.04, 22.04, or later) <br>
● Terminal access with sudo privileges <br>
● Internet connection for downloading packages
### Steps
#### 1. Update the Package Index
Ensure your system’s package index is up to date to avoid compatibility issues.
```bash
sudo apt update
```
#### 2. Install Redis
Install the Redis server and tool
```
sudo apt install redis-server
```

#### 3. Verify Installation
Check if Redis is installed by confirming the version.
```
redis-cli --version
```
This should output something like redis-cli 6.0.16 (version may vary).

#### 4. Start Redis Server
By default, the Redis server starts automatically after installation. Verify it’s running:
```
sudo systemctl status redis
```
Type q incase if prompt is not visible <br>
If it’s not running, start it:
```
sudo systemctl start redis
```

#### 5. Enable Redis on Boot
Ensure Redis starts automatically on system boot.
```
sudo systemctl enable redis
```

#### 6. Test Redis
Connect to the Redis server using the command-line interface: <br>
<b>redis-cli </b> <br>
At the Redis prompt ( for ex, 127.0.0.1:6379>), test connectivity: <br>
<b> ping </b> <br>
If successful, it returns PONG.
Type <b>exit</b> to return to the bash prompt.

#### 7. Configure Redis
The Redis configuration file is located at '/etc/redis/redis.conf'. To make changes (e.g., set
a password or bind address): <br>
```
sudo nano /etc/redis/redis.conf
```
● Example: To bind Redis to localhost only, find and set: bind 127.0.0.1 <br>
● Save and exit (Ctrl+O, Enter, Ctrl+X) <br>
● Restart Redis to apply changes:
```
sudo systemctl restart redis
```

#### 8. Secure Redis
For security, enable password authentication: <br>
● Open <b>/etc/redis/redis.conf</b> and find the requirepass directive. <br>
● Uncomment and set a strong password, e.g.:requirepass your_secure_password <br>
● Restart Redis:
```
sudo systemctl restart redis
```
Test authentication with
```
redis-cli
```
```
auth your_secure_password
```
Expected output: <b>OK </b>

#### 9. Verify Redis is Running
Check Redis is accessible and functioning
```
set testkey "Hello, Redis!"
get testkey
```
Expected output: "Hello, Redis!" <br>
Type exit to return to the bash prompt.

---

### Troubleshooting
● If Redis fails to start, check the logs:
```
journalctl -u redis
```
● Ensure port 6379 (default) is open if connecting remotely:
```
sudo ufw allow 6379
```