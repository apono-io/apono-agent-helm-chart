# apono-connector-helm-chart

## publish new version

- update app/chart version in `Chart.yaml`

- create a new packaged helm with `helm package .`

- move new packaged helm charts to project `apono-helm-charts/apono-connector`

- reindex the repo
`helm repo index --url https://apono-io.github.io/apono-helm-charts/ .`
- 