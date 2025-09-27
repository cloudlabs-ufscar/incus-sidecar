# Incus sidecar

Auxiliary services running in a Kubernetes cluster to support Incus deployments.

This repository contains manifests that are meant to be part of a GitOps workflow using [Flux](https://fluxcd.io). If you're not familiar with GitOps or Flux, we strongly suggest the reading of [this introduction](https://fluxcd.io/flux/concepts/) to make the most out of this project.

## Deploying

To start deploying these services, you must met the following prerequisites:

- The Flux CLI installed. We suggest checking [the official docs](https://fluxcd.io/flux/installation/) for instructions
- A Kubernetes cluster deployed and accessible from your machine. We suggest starting with [k3s](https://docs.k3s.io/quick-start) due to its simplicity and low overhead

With those requirements met, the next step is to deploy the Flux components to the Kubernetes cluster and configure them to sync the cluster state from this git repository. Flux calls this process "bootstrap".

For a comprehensive and up to date guide on how to perform the bootstrap process, check [the Flux docs](https://fluxcd.io/flux/installation/bootstrap/github/). The process consists of two steps:

1. Create a GitHub token with the required permissions for the GitHub repository and organization (the required permissions for repositories under organizations are described in [the Flux docs](https://fluxcd.io/flux/installation/bootstrap/github/#github-organization))
2. Run the bootstrap command with the generated token and the configuration pointing to this repository

The bootstrap command for this repository should look something like this:

```shell
GITHUB_TOKEN=<gh-token> flux bootstrap github \
                          --token-auth \
                          --owner=cloudlabs-ufscar \
                          --repository=incus-sidecar \
                          --branch=main \
                          --path=clusters/prod
```

After running the command, the Flux components should have been installed in the cluster under the `flux-system` namespace. You can check that by running the following command:

```shell
$ kubectl get all -n flux-system
```

There should also be a git source registered in Flux, usually referencing the latest commit of the `main` branch. You can check that by running the following command:

```shell
flux get sources git
```

With that, the setup is complete. Flux will automatically sync and apply any changes made to the git repository to the Kubernetes cluster. The applying of new changes usually takes a few minutes. If you need to troubleshoot something, the [Flux troubleshooting guide](https://fluxcd.io/flux/cheatsheets/troubleshooting/) is a great resource to get started.
