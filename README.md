# apono-agent-helm-chart

## Deploying

1. deploy helm chart to k8s -
```
$ helm install apono-agent <path-to-helm> \
    --set apono.token=<your_apono_token> \
    --set-file apono.resourcesConfig=<resources_config_file>
```

- `<path-to-helm>` - path to the .tar.gz file / to the helm chart directory
- `<your_apono_token>` - your secret apono token
- `<resources_config_file>` - your resources configurations file with all resources specified

2. redeploy helm chart after adding/removing/changing resources -

    this will recreate the pod and force it to load the new configuration

```
$ helm upgrade apono-agent <path-to-helm> \
    --set apono.token=<your_apono_token> \
    --set-file apono.resourcesConfig=<resources_config_file>
```

### Using Kubernetes Secrets Store CSI Driver

For supporting CSI in Apono first you need the install the [CSI driver](https://github.com/kubernetes-sigs/secrets-store-csi-driver) on your cluster.
CSI drivers can support different secret stores and for AWS secret manager you can use the [AWS Provider](https://github.com/aws/secrets-store-csi-driver-provider-aws).

pass to helm command the Secret Provider Class which describes the secrets you want to use and the service account that allow you to fetch the secrets.

```
    --set secretProviderClass=MySecretProviderClass \
    --set serviceAccount.create=false \
    --set serviceAccount.name=MyServiceAccount \
```

In MySecretProviderClass you can provide the secret and the specific key you want to use in the config using the JmesPath, for example:

```
objectName: "MySecret"
  jmesPath:
    - path: "username"
      objectAlias: "dbusername"
    - path: "password"
      objectAlias: "dbpassword"
  objectType: "secretsmanager"
```

After defining the secrets and the object alias you can use the keys as env variables in Apono resources config:

```
{
  "apono_resources": [
    {
      "name": "test_resource",
      "type": "postgresql",
      "config": {
        "hostname": "localhost",
        "port": 5432,
        "username": "${dbusername}",
        "password": "${dbpassword}",
        "dbname": "postgres",
        "sslmode": "disable"
      }
    }
  ]
}
```

You can see in the logs that the agent load the values:

```
{"level":"info","time":"","message":"loading secret dbpassword"}
{"level":"info","time":"","message":"loading secret dbusername"}
```


## publish new version

- update app/chart version in `Chart.yaml`

- create a new packaged helm with `helm package .`

- move new helm to `packaged/`

- reindex the repo
`helm repo index --url https://apono-io.github.io/apono-agent-helm-chart/ .`