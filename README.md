# Helm Chart for kubernetes agent
> Built with Helm v3.6.3

The main intention with this project is to provide an easy way of installing armory's Agent for Kubernetes

## Features

- Allows to easily deploy armory's agent for kubernetes with a single command
- Allows to customize the agent settings passing a custom file

## Requirements
- possibly helm v3.6.3 (haven't tested with anything prior)
- An existing kubernetes cluster and a namespace in case you don't want to use 'default'
- A secret containing a kubeconfig file in case an account has `serviceAccount: false`

## Usage

- `helm install release-name <path-of-the-chart> --namespace=your-namespace`
- `helm unisntall release-name --namespace=your-namespace`

> The release name is used across some of the kubernetes resources that get created, you can also add `--set image.imagePullSecrets=something` to use  docker registry credentials

## Quick Installation
> Note: you can also replace `helm install` by `helm template` in the below commands to get an output of the kubernetes manifests that are rendered e.g: `helm template armory-agent ...`

Create a namespace where the agent will be installed if it doesn't exist:

```sh
kubectl create namespace dev
```
Alternatively `--create-namespace` flag can be set in the `helm install` step

With the selected AWS profile that has access to the target cluster Create a kubeconfig file
> Note: skip this in case the agent is set to run in 'agent' mode (see https://docs.armory.io/docs/armory-agent/#deployment-modes)

```sh
aws eks update-kubeconfig --name <target_cluster> 
```

## Custom settings installation

###Infrastructure Mode

Accounts for which you plan to run on infrastructure mode (a.k.a  with `serviceAccount: false`) need a previously created secret from file.
For example:
```
kubectl create secret generic kubeconfig --from-file=/Users/armory/.kube/config -n dev
```

For those accounts with `serviceAccount: false`, update your `kubeconfigFiles.<accountName>` values in your settings file:

```
helm install armory-agent . --set config.clouddriver.grpc=localhost:9091,kubeconfigs.account1.file=config,kubeconfigs.account1.secret=mySecret
```

> Make sure `file` and `secret` match your secret file name and secret name respectively.

###Agent yml file

You can also install using your own agent settings file if you would like greater flexibility:
```
helm install armory-agent . --set-file agentyml=/Users/armory/.spinnaker/armory-agent.yml --namespace=dev
```

It is possible to pass an armory-agent.yml file and still override values e.g.
```
helm install --set-file agentyml=/Users/armory/.armory/armory-agent.yml --set config.clouddriver.grpc=localhost:7777 .
```

Every option supported in https://docs.armory.io/docs/armory-agent/agent-options/ is also supported under `config` e.g. `--set config.logging.level=debug`

## License

© 2021 Armory Inc. All rights reserved.

