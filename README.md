# APACHE2 Load Balancer

## Use Cases

* as reverse proxy to serve multiple (docker-) web apps on a single host
* as load balancer between multiple worker nodes
* as a combination of the first two things

## How to use

### Container

* create a local load balancer config file ```/path/loadbalancer.conf```
    ```bash
    [acme-app]                  # starts config part (name has no further meaning)
    uri=acme.de                 # the url your app should be available under
    cluster=acme-cluster        # name of load balancer cluster for acme-app
    nodes=[web1:80,web2:80]              # comma separated list of worker nodes (HOST|IP:PORT)
    ```
* create Dockerfile
    ```dockerfile
    FROM projektmotor/apache-load-balancer
    VOLUME /path/loadbalancer.conf:/etc/apache2/conf-loadbalancer/loadbalancer.conf
    ```  
* build image
    ```bash
    $ docker build -t acme-load-balancer .
    ```
* run the image
    ```bash
    $ docker run -it --rm --name acme-load-balancer-container acme-load-balancer
    ```

### Build-In Scripts

* add new VHOST (```apache-init-host.sh```)
    ```bash
    $ docker exec -it acme-load-balancer-container apache-init-host.sh HOST-URI CLUSTER-NAME
    ```
    * HOST-URI: the url your app should be available under 
    * CLUSTER-NAME: a name of load balancer cluster for your app (free choice, but needed for cluster & node conf)

* create new cluster (```apache-init-cluster.sh```)
    ```bash
    $ docker exec -it acme-load-balancer-container apache-init-cluster.sh CLUSTER-NAME
    ```
    * CLUSTER-NAME: the cluster name of your app (set in your vhost)

* add new worker node to existing cluster (```apache-add-cluster-node.sh```)
    ```bash
    $ docker exec -it acme-load-balancer-container apache-add-cluster-node.sh CLUSTER-NAME NODE
    ```
    * CLUSTER-NAME: the cluster name of your app (set in your vhost & cluster config)
    * NODE: a node config of format URI|IP[:PORT]

