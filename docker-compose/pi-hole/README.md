# Docker Pi-hole

Network-wide ad blocking via your own Linux hardware</strong>

## Documentation

The full documentation is available at https://docs.pi-hole.net.

## Note on Watchtower

We have noticed that a lot of people use Watchtower to keep their Pi-hole containers up to date. For the same reason we don't provide an auto-update feature on a bare metal install, you _should not_ have a system automatically update your Pi-hole container. Especially unattended. As much as we try to ensure nothing will go wrong, sometimes things do go wrong - and you need to set aside time to _manually_ pull and update to the version of the container you wish to run. The upgrade process should be along the lines of:
