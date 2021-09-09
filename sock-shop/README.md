# Sock Shop

![sock shop topology](https://raw.githubusercontent.com/microservices-demo/microservices-demo.github.io/0ac7e0e579d83ce04e14f3b0942f7a463b72da74/assets/Architecture.png)

### Sock Shop
1. Deploy sock shop application
    ```
    kubectl create ns sock-shop
    kubectl apply -f sock-shop-all-in-one.yaml
    ```
2. Determine how to access sock shop front end
    * If EKS cluster - expose front end as `LoadBalancer`
        ```
        kubectl -n sock-shop patch svc front-end -p '{"spec": {"type": "LoadBalancer"}}'
        ```
      * Retrieve front-end URL
        ```
        kubectl -n sock-shop get svc front-end -o jsonpath='{.status.loadBalancer.ingress[*].hostname}'
        ```
    * If RKE cluster - access the front end with `<node-IP>:<NodePort>`
      * where `<node-IP>` is any of the cluster's node's Public IPv4 address
      * where `<NodePort>` is 30001 (set in sock-shop-all-in-one.yaml)
      
* Deploy sock shop simulator in some VM outside of cluster
    ```
    # install necessary python packages
    pip install locust faker
    
    locust --headless -u 10 -r 1 --host=http://FRONT-END-URL -f locustfile.py
    ```
  
### Injecting Errors
1. Overflowing the Orders database
    * To inject an error into the orders database which will cause MongoDB to crash, simply run this job:
    ```
    kubectl apply -f mongo_insert.yaml
    ```
    * Then observe the orders-db pod and you should see it restarting numerous times due to error. 

    * To stop this mongo insertion job from running, simply run
    ```
    kubectl delete -f mongo_insert.yaml
    ```

2. Stopping an etcd node
    * For the second error where an etcd node is temporarily stopped, you will need to ssh into one of the nodes on your cluster which is running etcd.

    * Once you have done that, you can stop the etcd container:
    ```
    docker stop etcd
    ```
    * Once you have simulated that error, you can start the etcd container again
    ```
    docker start etcd
    ```
