# apono-agent-helm-chart

### publish new version

- update app/chart version in `Chart.yaml`

- create a new packaged helm with `helm package .`

- move new helm to `packaged/`

- reindex the repo
`helm repo index --url https://apono-io.github.io/apono-agent-helm-chart/ .`