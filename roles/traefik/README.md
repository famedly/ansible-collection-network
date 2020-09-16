ansible-traefik
===============

Deploys a containerized traefik, running in docker,
acting as a reverse proxy for your services.
This role supports the docker provider and file providers,
with the file provider watching files from `/opt/traefik/config.d/`.

Requirements
------------

This requires docker to be already installed.

Role variables
--------------

| Variable                             | Description                     |
|--------------------------------------|---------------------------------|
| traefik\_enable\_docker              | Enables docker support          |
| traefik\_acme\_email                 | Used for letsencrypt            |
| traefik\_enable\_api                 | Enable traefiks api             |
| traefik\_enable\_api\_dashboard      | Enable traefiks dashboard       |
| traefik\_enable\_api\_debug          | Enable traefiks debug api       |
| traefik\_enable\_api\_authentication | Enable basic auth on the api    |
| traefik\_api\_users                  | user:password for the api       |
| traefik\_api\_host                   | (V)Host for the api             |
| traefik\_version                     | traefik version                 |

License
-------

AGPLv3

Author Information
------------------

- Jan Christian Grünhage <jan.christian@gruenhage.xyz>
