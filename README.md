# Hybrid-Stack

# Building a High-Performance Hybrid Stack on Ubuntu 22.04

This guide provides a complete walkthrough for setting up a production-grade, resilient web server using a hybrid stack. The architecture is designed for high performance, security, and scalability, making it ideal for hosting demanding applications like WordPress, Magento, or WooCommerce.

## Core Architecture

Our stack is built on the principle of specialization, where each software component is configured to do what it does best.

-   **Nginx (The Gatekeeper):** Acts as the public-facing SSL Termination Proxy. It handles all encrypted traffic, serves static assets (CSS, JS, images) at maximum speed, and routes dynamic requests.
-   **Varnish (The Accelerator):** An in-memory cache that sits between Nginx and Apache. It stores copies of fully rendered pages to serve them instantly, drastically reducing server load and response times.
-   **Apache (The Workhorse):** The backend web server responsible for executing PHP code and processing `.htaccess` rules. It is protected by a Web Application Firewall (WAF).
-   **MariaDB (The Database):** The database engine for storing all application data.

### Architecture Flowchart

```mermaid
graph TD
    A[Internet User] --> B{Cloudflare};

    subgraph "Ubuntu 22.04 Droplet"
        B --> C{Nginx<br>SSL Proxy & Static Server};

        C -- "Request for Static File?<br>(e.g., .css, .jpg)" --> F[(Serve File Directly)];

        C -- "Request for Dynamic Page?<br>(e.g., WordPress URL)" --> D{Varnish Cache};

        D -- "Cache MISS" --> E{Apache + ModSecurity WAF<br>Processes PHP & .htaccess};
        D -- "Cache HIT" --> C;
        E -- "Generated HTML" --> D;
    end

    F --> B;
    C -- "Final HTML Response" --> B;

    style C fill:#27AE60,stroke:#2C3E50,stroke-width:2px,color:#fff
    style D fill:#3498DB,stroke:#2C3E50,stroke-width:2px,color:#fff
    style E fill:#E67E22,stroke:#2C3E50,stroke-width:2px,color:#fff
    style F fill:#95A5A6,stroke:#2C3E50,stroke-width:2px,color:#fff
