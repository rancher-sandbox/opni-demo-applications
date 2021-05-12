# Boutique

### Boutique
1. Deploy Boutique application
    ```
    kubectl apply -f kubernetes-manifests.yaml
    ```
2. Bring up the Boutique application on your browser
    ```
    kubectl get service/frontend-external
    ```
  
### Injecting Errors
1. Kill the productservicecatalog pod by scaling it down to 0
    * To inject this error:
    ```
    kubectl scale deploy productcatalogservice --replicas=0; date
    ```

    * To have the services go back to normal:
    ```
    kubectl apply -f kubernetes-manifests.yaml
    ```