# watchy

**WARNING** If you screw up and run this against a real cluster you have a good chance of breaking that cluster. Be sure to run watchy against the vcluster ONLY unless you are sure you are stress testing a cluster on purpose...

[watchy](https://github.com/evanfoster/watchy) is an app that can generate thousands of secrets and thousands of watchers of those secrets. This can basically overwhelm the Kubernetes API server is many scenarios. Does this really happen you ask? Yes, yes it does in a large multi-tenant environment. :( But there are other scenarios in which an out of control application can overwhelm an api server too.

## Run watchy

1. Switch to a vcluster environment
1. Install watchy
    ```sh
    kubectl apply -f watchy.yaml
    ```
1. Exec to watchy and create a large number of secrets
    ```sh
    kubectl exec -it watchy-0 -- bash
    export SECRET_COUNT=10000
    make create-secrets
    ```
1. Look to see how long it takes to get the secrets on the vcluster (from outside the container)
    ```sh
    time kubectl get secrets
    kubectl get secrets  1.45s user 0.24s system 33% cpu 4.995 total
    ```
1. Create some more secrets
    ```sh
    kubectl exec -it watchy-0 -- bash
    export SECRET_COUNT=50000
    make create-secrets
    ```
1. Look again to see how long it takes to get the secrets on the vcluster
   NOTE: This will probably break the vcluster; which is kind of the point of running this. The host cluster and other vcluster environments are fine but this vcluster is now busted. To fix, edit the StatefulSet on the host cluster and increase the memory limit.
    ```sh
    time kubectl get secrets
    kubectl get secrets  5.62s user 0.99s system 17% cpu 36.856 total
    ```
