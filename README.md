# kustomize-helm-concurrency-issue

Demonstrate issue with concurrency when doing multiple kustomize build operations at the same time with --helm-enabled

See: https://github.com/kubernetes-sigs/kustomize/issues/5271

We run `kustomize build` on all of our various overlays in our CI process. This allows us to test whether or not we have
broken any of our overlays. We have a lot of overlays, and we have a lot of CI jobs that run in parallel. We have
recently started using the `--helm-enabled` flag to allow us to use helm charts in our overlays. We have noticed that
when we run multiple `kustomize build` operations at the same time, we get errors like this:

```
[{1} overlays/cluster-1]  ➢ kustomize build --enable-helm overlays/cluster-1 > build/kustomize/overlays_cluster-1.yaml 
[{1} overlays/cluster-1] Error: accumulating resources: accumulation err='accumulating resources from '../../base': 'code/kustomize-helm-concurrency-issue/base' must resolve to a file': recursed accumulation of path 'code/kustomize-helm-concurrency-issue/base': Error: failed to untar: a file or directory with the name /code/kustomize-helm-concurrency-issue/base/charts/nri-bundle-5.0.25.tgz already exists 
[{1} overlays/cluster-1] Error: accumulating resources: accumulation err='accumulating resources from '../../base': 'code/kustomize-helm-concurrency-issue/base' must resolve to a file': recursed accumulation of path 'code/kustomize-helm-concurrency-issue/base': Error: failed to untar: a file or directory with the name /code/kustomize-helm-concurrency-issue/base/charts/nri-bundle-5.0.25.tgz already exists 
[{1} overlays/cluster-1] : unable to run: 'helm pull --untar --untardir code/kustomize-helm-concurrency-issue/base/charts --repo https://helm-charts.newrelic.com nri-bundle --version 5.0.25' with env=[HELM_CONFIG_HOME=/var/folders/sv/pwqjlk9557nfc5n444xhg8kr0000gp/T/kustomize-helm-25699378/helm HELM_CACHE_HOME=/var/folders/sv/pwqjlk9557nfc5n444xhg8kr0000gp/T/kustomize-helm-25699378/helm/.cache HELM_DATA_HOME=/var/folders/sv/pwqjlk9557nfc5n444xhg8kr0000gp/T/kustomize-helm-25699378/helm/.data] (is 'helm' installed?): exit status 1 
```

This repo provides an example of this error.

## How to reproduce

1. Clone this repo
2. Run (sometimes multiple times)
```shell
# Remove the existing charts file as it is not present in CI
rm -rf base/charts
# Run the build script
./kustomize-build
```

### GHA Alternative

If you want to, you can run the GHA workflow: https://github.com/greenkiwi/kustomize-helm-concurrency-issue/actions/workflows/ci.yaml
