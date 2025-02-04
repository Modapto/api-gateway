# modapto-api-gateway

## Overview

API Gateway utilizes Traefik reverse proxy to route specific domains / subdomains to MODAPTO components.

It provides load balancing and firewall rules to enable or forbid specific communication from outside services. It secures the HTTP communication via HTTPS protocol.

## Table of Contents

1. [Installation](#installation)
2. [Deployment](#deployment)
3. [Usage](#usage)
4. [LICENSE](#license)
5. [Contributors](#contributors)

### Installation

Clone the repository:

```sh
git clone https://github.com/Modapto/api-gateway.git
cd api-gateway
```

### Deployment

1. Install or make sure Docker is running

2. Create a hashed password for a specific user and update the traefik_dynamic.toml file under the config folder with the following:

     ```sh
    # Generate a hashed password using htpasswd
    htpasswd -nb [user] [password]

    # Copy the generated password and configure the dynamic config
    [hostPath] = URL of the Traefic Dashboard (if enabled)
    [user]:[hashedPassword] = Copy the contents of the generated hashed value

    # Configure your email on traefik.toml
    [email]: Your personal email

   ```

3. Deploy API Gateway from Docker Compose:

    ```sh
    docker compose up -d
   ```

4. After some time Traefik dashboard will be available on: `https://[hostPath]`.

5. To terminate Docker compose simply run:

    ```sh
    docker compose down
    ```

### Usage

For each new component that will de deployed through API Gateway we must define the following labels in each docker compose configuration:

```sh
        - traefik.enable=true
        - traefik.http.routers.[containerName]=true
        - traefik.http.routers.[containerName].rule=Host(`[desiredURLPath]`)
        - traefik.http.routers.[containerName].tls=true
        - traefik.http.routers.[containerName].tls.certresolver=lets-encrypt
        - traefik.http.middlewares.securityHeader.headers.sslredirect=true
        - traefik.http.routers.[containerName].middlewares=securityHeader
        - traefik.http.services.notification-center.loadbalancer.server.port=[containerPort]

        # Optionally we can configure a PathPrefix in the Host Role
        - traefik.http.routers.[containerName].rule=Host(`[desiredURLPath]`) && PathPrexix(`[desiredPathPreix]`)
```

## LICENSE

This project has received funding from the European Union's Horizon 2022 research and innovation programm, under Grant Agreement 101091996.

For more details about the licence, see the [LICENSE](LICENSE) file.

## Contributors

- Alkis Aznavouridis (<a.aznavouridis@atc.gr>)
