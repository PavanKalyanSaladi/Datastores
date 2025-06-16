
# DevOps Unit-1

## Table of Contents

- [Exercise 1: Installing Redis](#exercise-1-installing-redis)
- [Exercise 2: Installing KeyDB](#exercise-2-installing-keydb)
- [Exercise 3: Manage E-commerce Cart Sessions and Authentication Tokens](#exercise-3-manage-e-commerce-cart-sessions-and-authentication-tokens)
- [Exercise 4: Performance Benchmarks](#exercise-4-performance-benchmarks)

---

## Exercise 1: Installing Redis

### Prerequisites

- Ubuntu system (e.g., 20.04, 22.04, or later)
- Terminal access with `sudo` privileges
- Internet connection

### Steps

1. **Update Package Index**

```bash
sudo apt update
```

2. **Install Redis**

```bash
sudo apt install redis-server
```

3. **Verify Installation**

```bash
redis-cli --version
```

4. **Start Redis Server**

```bash
sudo systemctl status redis
sudo systemctl start redis
```

5. **Enable Redis on Boot**

```bash
sudo systemctl enable redis
```

6. **Test Redis Connection**

```bash
redis-cli
ping
# Expected: PONG
exit
```

7. **Configure Redis**

```bash
sudo nano /etc/redis/redis.conf
# Set bind to 127.0.0.1
sudo systemctl restart redis
```

8. **Secure Redis**

```bash
# Edit requirepass in /etc/redis/redis.conf
sudo systemctl restart redis
redis-cli
auth your_secure_password
# Expected: OK
```

9. **Verify Functionality**

```bash
set testkey "Hello, Redis!"
get testkey
# Expected: "Hello, Redis!"
exit
```

### Troubleshooting

```bash
journalctl -u redis
sudo ufw allow 6379
```

---

## Exercise 2: Installing KeyDB

### Prerequisites

- Ubuntu (20.04 or 22.04)
- Sudo/root privileges
- Internet access

### Steps

1. **Add KeyDB Repository**

```bash
echo "deb https://download.keydb.dev/open-source-dist $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/keydb.list
```

2. **Add GPG Key**

```bash
sudo wget -O /etc/apt/trusted.gpg.d/keydb.gpg https://download.keydb.dev/open-source-dist/keyring.gpg
```

3. **Update Package List**

```bash
sudo apt update
```

4. **Install KeyDB**

```bash
sudo apt install keydb
```

5. **Start and Enable Service**

```bash
sudo systemctl start keydb
sudo systemctl enable keydb
```

6. **Verify Installation**

```bash
keydb-cli ping
# Expected: PONG
```

7. **Secure KeyDB**

```bash
# Edit /etc/keydb/keydb.conf
# Set requirepass your_secure_password
# Use UFW to restrict access
sudo ufw allow from <trusted_ip> to any port 6379
sudo ufw enable
sudo systemctl restart keydb
```

### Troubleshooting

- Build dependencies
- Port conflicts: `sudo netstat -tuln | grep 6379`
- Service status: `journalctl -u keydb.service`

---

## Exercise 3: Manage E-commerce Cart Sessions and Authentication Tokens

### Steps

1. **Set Up the Environment**

```bash
sudo apt-get install keydb
pip install keydb cryptography
```

2. **Run the Application**

```bash
python keydb_ecommerce_sessions.py
```

### Expected Output

- Unique session token (UUID)
- Unique cart ID
- Cart contents: 2 items with prices
- Session data with TTLs in KeyDB

---

## Exercise 4: Performance Benchmarks

### Objective

Benchmark KeyDB using `memtier_benchmark`.

### Prerequisites

- Ubuntu/Debian system with sudo
- KeyDB installed
- Separate client-server machines recommended

### Steps

#### 1. Install memtier_benchmark

```bash
sudo apt-get install build-essential autoconf automake libpcre3-dev libevent-dev pkg-config zlib1g-dev
git clone https://github.com/RedisLabs/memtier_benchmark.git
cd memtier_benchmark
autoreconf -ivf
./configure
make
sudo make install
memtier_benchmark --version
```

#### 2. Configure KeyDB

```bash
sudo nano /etc/keydb/keydb.conf
# Set maxmemory, maxclients, timeout, requirepass, server-threads
sudo systemctl restart keydb
```

#### 3. Prepare Benchmark Environment

```bash
keydb-cli ping
sudo cpupower frequency-set -g performance
```

#### 4. Run Benchmarks

```bash
keydb-server --server-threads 4 --requirepass mypassword123 --port 6379 --daemonize yes

memtier_benchmark -s 127.0.0.1 -p 6379 --authenticate=mypassword123 --threads=4 --clients=50 --test-time=60
```

Additional examples:

```bash
# SET operations
--command="SET __key__ __data__" --data-size=100

# GET operations
--command="GET __key__" --random-data

# Enable pipelining
--pipeline=10

# Key pattern
--key-pattern=S:S
```

#### 5. Analyze Results

Sample output format:

```
Type   Ops/sec   Hits/sec   Misses/sec   Latency   KB/sec
Sets   45000.00  ---        ---          0.200     3500.00
Gets   43000.00  43000.00   0.00         0.190     3200.00
Total  88000.00  43000.00   0.00         0.195     6700.00
```

Check:

```bash
keydb-cli INFO ALL
```

Key metrics:

- `used_cpu_sys`
- `used_memory`
- `instantaneous_ops_per_sec`
- `total_net_input_bytes`, `total_net_output_bytes`

#### 6. Optimize

- Adjust data size, thread count, client count
- Tune KeyDB config
- Monitor system performance: `htop`, `iotop`, `nload`