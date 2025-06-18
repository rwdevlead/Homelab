# Watchtower

A process for automating Docker container base image updates.
<br/>

### [Note on Watchtower in regaurds to PiHole](https://github.com/pi-hole/docker-pi-hole/#note-on-watchtower)

## Quick Start

With watchtower you can update the running version of your containerized app simply by pushing a new image to the Docker Hub or your own image registry.

Watchtower will pull down your new image, gracefully shut down your existing container and restart it with the same options that were used when it was deployed initially. Run the watchtower container with the following command:

## Documentation

The full documentation is available at https://containrrr.dev/watchtower.
