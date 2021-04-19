# opni-demo-applications

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