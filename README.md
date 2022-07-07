# vcluster-meetup-demo

The demo stuff for the [loft.sh vcluster meetup](https://www.meetup.com/loft-meetup-san-francisco/events/286690864/) on July 26th, 2022.

## Links

### Quick links

- [vcluster](https://www.vcluster.com/)
- [cluster-api](https://cluster-api.sigs.k8s.io/)
- [cluster-api-provider-vcluster](https://github.com/loft-sh/cluster-api-provider-vcluster)
- [Argo CD](https://argoproj.github.io/cd/)

### View the presentation slides

TBD slide link goes here when they are finished...

### View the recording

TBD hopefully there is a recording... if not, make one...

## Quick & dirty steps for how to run this demo

Warning: Thar be dragons here until I clean this up before the meetup...

1. [Install clusterctl](https://cluster-api.sigs.k8s.io/user/quick-start.html#install-clusterctl)
1. Modify `~/.cluster-api/clusterctl.yaml` and add the vcluster provider
    ```yaml
    providers:
    - name: vcluster
        url: https://github.com/loft-sh/cluster-api-provider-vcluster/releases/latest/infrastructure-components.yaml
        type: InfrastructureProvider      
    ```
1. Make sure your `KUBECONFIG` is pointing to the cluster you want to have Argo CD and the vcluster(s) installed on
1. [Install Argo CD](https://argoproj.github.io/cd/) on the cluster
1. Initialize cluster-api `clusterctl init --infrastructure vcluster`
1. Create a namespace to run in `kubectl create ns vcluster`
1. Fork this repo so you make modifications and test out the process
1. Take the `argocd/applicationset.yaml` and modify to set any helm values as appropriate. e.g., set the `base_domain_name` and `ingress_class` if you want (I prefer to use some form of ingress over `vcluster connect`)
    - NOTE: If you don't use Contour for your ingress, you'll want to change from `HTTPProxy` to your ingress object of choice for the helm chart.
    - Be sure to update the `Chart.yaml` with a new version number with each change
    - Point the `spec.generators.pullRequest.github.owner` & `spec.generators.pullRequest.github.repo` to your forked repo
    - Update the `spec.template.spec.source.repoURL` to your forked repo
1. Apply the `ApplicationSet` by running `kubectl apply -f applicationset.yaml -n argocd`
1. Create a PR against your forked repo
1. Check that a new Argo CD `Application` has been automatically created `kubectl get applications -n argocd`
1. Check that a new vcluster is being created `kubectl get cluster -n vcluster` by the cluster-api
1. Get the `KUBECONFIG` for the new vcluster `clusterctl get kubeconfig <cluster name from above> --namespace vcluster > kubeconfig.yaml`
1. Use the new vcluster `export KUBECONFIG="$(pwd)/kubeconfig.yaml"`
1. Profit!
