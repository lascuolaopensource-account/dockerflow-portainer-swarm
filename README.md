# Docker Flow Proxy with Let's Encrypt and Portainer

Swarm setup at La Scuola Open Source for reverse-proxying per service and Portainer interface.
Based on [Docker Flow Proxy](https://github.com/docker-flow/docker-flow-proxy.git) with [Docker Swarm Listener](https://github.com/docker-flow/docker-flow-swarm-listener), using [harmburml/docker-flow-letsencrypt](https://github.com/hamburml/docker-flow-letsencrypt) for certificates handling, [jwilder/whoami](https://github.com/jwilder/whoami) for testing and [Portainer](https://github.com/portainer/portainer) to manage the Swarm.

## Setup
### Create the swarm
This command will start the Swarm and join it as manager:

    sudo docker swarm init
    
Then use the printed command to let every node join the swarm as worker.
You can retrieve the manager token with:

    sudo docker swarm join-token manager

### Create overlay network for proxy
Create the proxy network to let services communicate with reverse-proxy:

    sudo docker network create -d overlay proxy

### Create letsencrypt folder to store certificates
For now the setup needs a folder to store container. It will probably be implemented with Volumes soon.

    sudo mkdir /etc/letsencrypt/

## Configure Proxy

### Lock letsencrypt-companion to main node
You need to set your Leader Manager node ID in `proxy/flow.yml`.
You can retrieve it with:
    
    sudo docker node ls

Copy and paste the ID in `proxy/flow.yml`at line:
> constraints: [node.id == paste node id here ]

### Configure your Domains
You can set your domains name as DOMAIN1, DOMAIN2, etc.
If you tweak them you should also match 'em in each service labels.
Be sure your DNS and routerd are forwarding requests to one of your Swarm nodes.

## Depoloy Stacks

Deploy the Docker Flow Proxy (with letsencrypt) stack to start generating certificates:

    sudo docker stack deploy -c flow.yml proxy

Deploy Portainer stack:

    sudo docker stack deploy -c portainer-agent-stack.yml portainer

Deploy the Whoami test service:
    
    sudo docker stack deploy -c who.yml who

## Sbam
You can now access portainer at:
https://portainer.lascuolaopensource.xyz:9000

and Whoami:
https://admin.lascuolaopensource.xyz
