# Helm intro

## Get the cluster running

- [Install kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
- [Install minikube](https://minikube.sigs.k8s.io/docs/start/)
- [Install helm](https://helm.sh/docs/intro/install/#from-script)

```bash
# start you local minikube cluster
minikube start

# check that the cluster is running with
minikube status
kubectl get ns  # ns is shorthand for namespace
```

## Deploy your first chart

```bash
# create the namespace for the chart
kubectl create ns helm-intro

# install the given chart into the helm-intro namespace
helm install intro . -f values.yaml -n helm-intro
```

This installed the `REVISION: 1` of this chart under the name
`intro` (that can be choosen) and returned the content of the
file `templates/NOTES.txt` with some notes for the user.

There are a few ways to look at the revision now.

```bash
# get the manifests currently deployed
helm get manifest intro -n helm-intro

# analyze a specific helm revision
helm status intro -n helm-intro -o yaml --revision 1
```

Now let's checkout the app we've deployed.
In `templates/deployment.yaml` a deployment is defined with
a pod template for a default nginx server.
In `templates/service.yaml` there is a service called `nginx-service`
exposing the pod on a node port.
In `templates/configmap.yaml` there is the `index.html` page
replacing the nginx default (mounted in the deployment).

```bash
# minikube has this neat way of getting the url for the service
minikube service nginx-service --url -n helm-intro
```

Let's see if there is `Welcome to the helm intro!` on this url
and whether it looks very much green.

## Upgrade the chart

Seems like the designer thinks fuchsia seems to a better color 😱,
let's change the nginx `backgroundColor` in `values.yaml` to `fuchsia`.

To verify the change before upgrading this chart now, you can
use this super cool plugin to check what's gonna happen.

```bash
# download the plugin (this will simply place the repo into
# a helm plugin directory)
helm plugin install https://github.com/databus23/helm-diff

# let's use the plugin to see what would happen if we were
# to upgrade this chart
helm diff upgrade intro . -n helm-intro -f values.yaml
```

Looks good, right? Only the background color changed in our
configmap plus the checksum in the deployment annotations.
Time to actually do the change.

```bash
helm upgrade intro . -n helm-intro -f values.yaml
```

To check what happend between revision 1 and 2, let's use this
awesome plugin again!

```bash
helm diff revision -n helm-intro intro 1 2
```

## Rollback

Oh snap! Fuchsia looks terrible 😋.
Let's rollback, but check the diff in advance - just to be sure...

```bash
# diff for the rollback from the current revision 2 to the revision 1
helm diff rollback -n helm-intro intro 1
```

This looks good, rolling back will again set the color to green.
Let's go with that change and rollback the release.

```bash
helm rollback -n helm-intro intro 1
```

The history shows now also that the revision 3 was a rollback to
revision 1.

```bash
helm history -n helm-intro intro
```

## Destroy everything

Let's get rid of that stuff, enough is enough:

```bash
# uninstall the chart
helm uninstall -n helm-intro intro

# remove the namespace
kubectl delete ns helm-intro

# delete the minikube cluster
minikube delete
```

## Further resources

- [Chart structure](./chart-structure.md)
- [Helm lint](https://helm.sh/docs/helm/helm_lint/)
- [Kube-Score](https://github.com/zegl/kube-score#example-with-helm)
