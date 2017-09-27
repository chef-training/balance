# Balance

This example habitat application that takes a simple web application (webapp) and a load balancer (haproxy). Two webapps are launched and then bound to the haproxy through the backend binding.

## Build

    $ hab pkg build webapp
    $ hab pkg export docker cheftraining/webapp
    $ hab pkg build haproxy
    $ hab pkg export docker cheftraining/haproxy

### Within the Studio

    $ hab studio enter
    $ cd webapp
    $ build
    $ hab pkg export docker cheftraining/webapp
    $ cd ../haproxy
    $ build
    $ hab pkg export docker cheftraining/haproxy

## Run

    $ docker-compose up

To verify that the load balancer is working execute:

    $ curl localhost:8000/cgi-bin/hello-world

> Run this a few times to see the different webapp's return their specifics


## Adding More Web Applications

The docker compose file creates a default network that *webapp-1*, *webapp-2*, and the *load_balancer* join. The name of the network is based on the current directory (likely balance_default).

To launch additional web applications you first need to get the Docker Image ID of the webapp:

    $ docker images | grep cheftraining/webapp
      cheftraining/webapp            latest              5ab601122358        7 days ago          185MB

> In the above example output the docker image ID would be 5ab601122358

Then with that docker image ID you can launch additional webapps that join the same network by running:

    $ docker run --network=balance_default DOCKER_IMAGE_ID --peer webapp-1

> The peer flag at the end is passed to the Habitat Supervisor which needs to specify the name of at least one of the instances specified in the docker-compose.yml.

Alternatively there is another docker-compose file in the directory that can be executed and will create two additional webapps that join the network.

    $ docker-compose -f more-webapps.yml up
