---
author: <1>
title: Grafana DashBoard Provisioning
date: 2023-02-10
categories: [Grafana]
tags: [Grafana]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

### Grafana DashBoard


> grafana 서비스를 최초 실행시 dashboard Provisioning하는 방법

1. values.yaml파일 이용

2. grafana api 이용 따로 관리한 json파일을 이용 upload

```shell
#!/bin/bash
{% raw %}
export HOST=$(kubectl get -n traefik ingressroute --template '{{range .items}}{{(index .spec.routes 0).match}}{{"\n"}}{{end}}' | grep -o "grafana\.[^']*" | cut -d "\`" -f 1)
export ACCESS_TOKEN=$(curl -X POST -H "Content-Type: application/json" -d '{"name":"apikeycurl", "role": "Admin"}' https://admin:prom-operator@"${HOST}"/api/auth/keys | jq -r '.key')
export DirectoryPath=$(dirname "$0")
export DASHBOARD=$(ls "$DirectoryPath")

for i in $DASHBOARD; do
  if [[ "$i" =~ .*"json" ]]; then
    echo https://"${HOST}"/api/dashboards/db
    curl -XPOST -i  https://"${HOST}"/api/dashboards/db --data-binary @"${DirectoryPath}/${i}" -H "Content-Type: application/json"  -H 'Authorization: Bearer '${ACCESS_TOKEN}''
  fi
done
{% endraw %}
```
