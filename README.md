# Server Template

A production-ready template for running an HAProxy reverse proxy with automatic SSL (Let's Encrypt), Nginx static site, and rsyslog logging, all orchestrated with Docker Compose.

## Features
- **HAProxy**: SSL termination, ACME HTTP-01 challenge support, and flexible routing.
- **Nginx**: Serves as a static site or fallback 404 page.
- **rsyslog**: Centralized logging for HAProxy.
- **Let's Encrypt**: Automated SSL certificate management.

## Directory Structure
```
haproxy/
  ├── docker-compose.yml      # Main Docker Compose file
  ├── haproxy.cfg             # HAProxy configuration
  ├── sites.map.example       # Example domain-to-backend mapping
  ├── 404.html                # Custom 404/forbidden page
  ├── .gitignore              # Ignore sensitive/config files
  └── data/                   # Persistent data (certs, logs, etc.)
```

## Prerequisites
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/)
- An external Docker network named `server-network` (see below)

## Quick Start
1. **Clone the repository:**
   ```sh
   git clone <your-repo-url>
   cd server-template/haproxy
   ```

2. **Create the Docker network (if not already present):**
   ```sh
   docker network create server-network
   ```

3. **Prepare configuration files:**
   - Copy `sites.map.example` to `sites.map` and edit as needed:
     ```sh
     cp sites.map.example sites.map
     # Edit sites.map to map domains to backends
     ```
   - Copy `.env.example` to `.env` and fill in the required values:
     ```sh
     cp .env.example .env
     # Edit .env to set your email and certificate domains
     ```
     - **EMAIL**: Your email address for Let's Encrypt notifications.
     - **STAGING**: Set to `true` to use Let's Encrypt's staging environment (for testing, avoids rate limits). Use `false` for production.
     - **CERT1**: The primary domain(s) for which to issue a certificate (e.g., `example.com,www.example.com`).

4. **Start the stack:**
   ```sh
   docker-compose up -d
   ```

## Configuration
- **HAProxy**
  - Configured via `haproxy.cfg`.
  - Uses `sites.map` for domain-to-backend routing.
  - Handles ACME HTTP-01 challenges for Let's Encrypt automatically.
- **Nginx**
  - Serves the `404.html` as the default page.
- **rsyslog**
  - Receives logs from HAProxy for monitoring and troubleshooting.
- **Persistent Data**
  - Certificates and logs are stored in `data/` (ignored by git).

## Customization
- Add your own backend services to the `server-network` Docker network.
- Update `sites.map` to route domains to your services.
- Replace `404.html` with your own error or landing page if desired.

## Security Notes
- Do **not** commit your `.env`, `sites.map`, or any sensitive data to version control.
- Review and adjust `haproxy.cfg` and `docker-compose.yml` for your production needs.

## License
MIT
