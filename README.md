# nginx-learn
NGINX Tutorial: Load Balancing, Caching, and SSL

NGINX is a powerful and flexible software foundational to modern web infrastructure. Originally a web server that assembled pages and responded to browser requests, it has expanded into a highly popular proxy server solution.

## NGINX Fundamentals and Main Use Cases

NGINX is renowned for being faster and more lightweight than Apache, especially in serving static files, with a relatively easy configuration process. Its capabilities were extended to meet the growing demands of the web, including handling thousands or millions of requests. Key use cases include:

### 1. Load Balancing and Proxying
- Acts as a load balancer by distributing incoming requests among multiple backend servers (via `upstream` configurations).
- Supports algorithms like:
  - **Round Robin** (default): Cyclic distribution.
  - **Least Connections**: Forward requests to the least busy server.

### 2. Caching
- Stores responses (e.g., static articles) to serve subsequent requests rapidly, reducing backend load.

### 3. Security and Shielding
- Acts as a security layer, exposing only the NGINX proxy rather than all backend servers.

### 4. Handling Encrypted Traffic (SSL/HTTPS)
- Accepts encrypted requests, ensures secure communication with SSL/TLS configuration, and defaults to HTTPS.

### 5. Compression
- Compresses large files (images, videos) to save bandwidth and accelerate delivery.

### 6. Kubernetes Ingress Controller
- Commonly used in containerized environments to route traffic internally, enabling microservice architectures.

---

## NGINX as a Reverse Proxy & Configuration Principles

NGINX functions as a reverse proxy by receiving client requests and forwarding them to backend servers. Its behavior is controlled entirely through the configuration file, which employs directives within contexts such as `events`, `http`, `server`, `location`, and `upstream`.

### Key Configuration Elements

- **Worker Processes & Connections**
  - `worker_processes`: Number of worker processes (typically matches CPU cores).
  - `worker_connections`: Number of simultaneous connections per worker within the `events` block.

- **HTTP Block**
  - Contains core configurations, including server routing.

- **Server & Location Blocks**
  - Define listening ports, protocols, and request handling logic.

- **Upstream Configuration**
  - Lists backend servers to distribute traffic among (e.g., Node.js instances).

- **Proxy Pass & Headers**
  - `proxy_pass`: Forward traffic to backend.
  - Forward client info using headers (`proxy_set_header host`, `X-Forwarded-For`, etc.).

---

## Secure HTTPS Configuration

Steps to set up SSL/TLS on NGINX:

1. Generate an SSL certificate and private key.
2. Configure `server` block to listen on port 443 with `ssl`.
3. Use `ssl_certificate` and `ssl_certificate_key` directives.
4. Redirect HTTP traffic (port 80) to HTTPS with a 301 redirect.

---

## Example: Basic NGINX Reverse Proxy with Load Balancing and SSL

```nginx
http {
  upstream app_servers {
    server 127.0.0.1:3000;
    server 127.0.0.1:3001;
  }

  server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$host$request_uri;
  }

  server {
    listen 443 ssl;
    server_name yourdomain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location / {
      proxy_pass http://app_servers;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
