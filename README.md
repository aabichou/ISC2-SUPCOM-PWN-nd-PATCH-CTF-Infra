# CTF-Challenges-ISC2_CTF
This is the infra repository for ISC2 CTF challenges.

This was used because we wanted to utilize old hardware we had available, and cool technology like FRP.

The setup relies on 2 Servers , one having a public IP address (can be a cheap VPS) and one being on a private network (like a home server).

The public server runs FRP server (frps) and the private server runs FRP client (frpc). The FRP client connects to the FRP server, allowing access to services running on the private server from the public server through a secure tunnel.

## FRP Server (frps) Setup
The FRP server is set up using Docker Compose. The configuration file `frps.json` defines the settings for the FRP server, including the bind address and port.
The `compose.yaml` file defines the Docker Compose setup for the FRP server, including volume mounts for configuration and logs, and environment variables for dynamic DNS updates.
The FRP server listens on port 7000 for incoming connections from FRP clients (for now it is not using any kind of authentication, but it can be configured to do so).

## FRP Client (frpc) Setup
The FRP client is also set up using Docker Compose. The configuration file `frpc.json` defines the settings for the FRP client, including the server address and port to connect to.

`frpc.json` also defines the services to be exposed through the FRP server, such as SSH and HTTP services.

In our case and for ease of use instead of exposing multiple services through FRP statically we are directing all the HTTP and HTTPS traffic to a traefik instance running on the private server, which will then route the traffic to the appropriate containers based on the domain names. It also handles Certificate generation through Let's Encrypt as well as SSL termination.

Traefik has the cool feature of being able to dynamically update its configuration based on labels defined in the Docker Compose files of the services it manages, which makes it very flexible and easy to use in a dynamic environment like this one.

## Dynamic DNS
To handle dynamic IP addresses on the private server side, we are using a Cloudflare Dynamic DNS service. This service updates the DNS records on Cloudflare with the current public IP address of the private server, ensuring that the FRP client can always connect to the FRP server using a consistent domain name.

As for services most of the services are web applications that are vulnerable by design, and are used for CTF challenges. They are all set up to be accessible through the FRP server using subdomains managed by Traefik.

## References
- [FRP (Fast Reverse Proxy)](https://github.com/fatedier/frp)
- [Traefik](https://traefik.io/)
- [Cloudflare Dynamic DNS](https://www.cloudflare.com/en-gb/dynamic-dns)
- [Docker](https://www.docker.com/)

