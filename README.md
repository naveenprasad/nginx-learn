# nginx-learn
NGINX Tutorial: Load Balancing, Caching, and SSL

NGINX is presented in the sources as powerful and flexible software that serves as a foundational technology in modern web infrastructure. Initially, NGINX served as web server software that ran on a server machine, assembling pages and responding to requests from a browser. However, its functionality was extended, making it a highly popular proxy server solution as well.
NGINX is known for being faster and more lightweight than the older Apache web server, particularly excelling at serving huge amounts of static files, and offering a relatively easy configuration process.
NGINX Fundamentals and Main Use Cases
The necessity for NGINX's expanded functionality arose when the web grew popular, demanding server solutions that could handle thousands or millions of requests per website, exceeding the technical limit of a single server.
The sources outline several critical use cases and functionalities for NGINX, especially when acting as a proxy:
1. **Load Balancing and Proxying**: NGINX acts as a load balancer by sitting at the entry point (like a concierge) and distributing incoming client requests among multiple backend web servers (often referred to as an "Upstream" configuration). Proxying means accepting browser requests on behalf of the web servers. NGINX can be configured to use specific load balancing algorithms, such as Round Robin (the default, distributing requests cyclically and equally) or Least Connections (forwarding the request to whichever server is least busy or has the fewest active connections).
2. **Caching**: NGINX can be configured to cache responses (e.g., static articles). This feature allows NGINX to serve the stored final copy to subsequent requests, meaning it does not have to hit the database or the web servers every time, which improves efficiency.
3. **Security and Shielding**: An extremely important function of the NGINX proxy is to act as a shield or security layer for the backend web servers. By having only the NGINX proxy publicly accessible, it provides a single entry point, which dramatically reduces the security attack surface compared to exposing all 100 backend servers directly to the public.
4. **Handling Encrypted Traffic (SSL/HTTPS)**: NGINX can be configured to accept encrypted traffic using SSL encryption. It is standard practice to configure NGINX to only accept encrypted requests and deny any that are not. This is essential for secure communication (HTTPS), as the data exchanged is encrypted, protecting it even if an attacker monitors the transmission.
5. **Compression**: NGINX can be configured to compress large images or video files. This saves bandwidth for both the client receiving the large files and the server sending the content, ensuring faster delivery. It also supports sending responses in chunks instead of the entire file at once.
6. **Kubernetes Ingress Controller**: NGINX is flexible and popular in containerized environments. It is one of the most popular solutions for the Kubernetes Ingress controller functionality, where it acts as a proxy and load balancer for a cluster. This setup allows for intelligent routing; for instance, routing traffic to a specific microservice if the request URL includes a particular segment (e.g., routing /online-card to the online card microservice).

**NGINX Reverse Proxy and Configuration Context**
The primary contemporary role discussed in the source is configuring NGINX as a reverse proxy. A reverse proxy sits on the server side as the entry point for all client requests, load balancing or forwarding them to the backend servers, and then passing the response back to the client.
The behaviour of NGINX, whether acting as a web server or a proxy server, is entirely defined within the NGINX configuration file.

**Key Configuration Concepts**
The NGINX configuration file uses key-value configurations called directives. These directives are placed inside enclosing blocks called contexts (such as events or http).
Configuration elements include:

**Defining Worker Processes and Connection**s: The worker_processes directive sets how many worker processes NGINX should start, affecting its ability to handle high request load, often recommended to equal the number of available CPU cores. The worker_connections directive, found within the events context, defines how many simultaneous connections each worker process can handle.
• **The HTTP Block**: The core logic for handling requests resides in the http block, which contains the main configurations, including the server block.
• **Defining Servers and Locations**: The server block defines where NGINX listens (e.g., on a specific port like 8080) and the location block defines what NGINX should do with a request arriving at a specific URL path.
• **Defining Upstream Backends**: To configure NGINX as a proxy, you must define an Upstream block (e.g., nodejs_cluster) that lists the addresses and ports of all backend applications (servers/replicas).
• **Proxying the Traffic**: The proxy_pass directive is used within a location block to explicitly tell NGINX to forward the traffic to a defined Upstream.
• **Handling Client Information (Headers)**: Since the backend application receives requests only from NGINX (the shield) and not directly from the client, NGINX must be configured to pass crucial information to the backend via headers. 
Important proxy headers include:
    ◦ proxy_set_header host: Forwards the original requested URL/host to the backend so it can handle proper routing.
    ◦ Passing the client's original IP address: Setting the real client IP address, stored by NGINX in the remote_address variable, is vital for logging, security, and analytics on the backend.

**Secure HTTPS Configuration**
A crucial configuration in the context of the reverse proxy is enabling SSL/HTTPS. This involves:
1. **Generating Certificates**: Requiring an SSL certificate, which validates the server and contains the public key, alongside a private key.
2. **Listening on HTTPS Por**t: Configuring a server block to listen on the default HTTPS port, Port 443, and specifying ssl.
3. **Specifying Key Location**s: Using the ssl_certificate and ssl_certificate_key directives to point NGINX to the location of the public key (certificate) and private key files, respectively.
4. **Redirection**: To ensure all traffic is secure, NGINX can be configured to listen on the insecure HTTP port (Port 80) and automatically redirect clients to the HTTPS endpoint using a 301 redirection HTTP response.
After any configuration change, NGINX must be reloaded or restarted to ensure the new configuration is loaded and used
