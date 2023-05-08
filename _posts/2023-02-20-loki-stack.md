---
author: <1>
title: Loki-Stack.yaml
date: 2023-02-20
categories: [Loki]
tags: [Loki]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

```yaml
loki:
  enabled: true
  persistence:
    storageClassName: hostpath
    enabled: true
    accessModes:
      - ReadWriteMany
    size: 10Gi
  config:
    compactor:
      delete_request_cancel_period: 1h
      compaction_interval: 10m
      retention_enabled: true
      retention_delete_delay: 2h
      working_directory: /data/retention
    limits_config:
      retention_period: 720h
    memberlist:
      bind_addr:
        - ${MY_POD_IP}
    query_scheduler:
      max_outstanding_requests_per_tenant: 10000
  env:
    - name: MY_POD_IP
      valueFrom:
        fieldRef:
          fieldPath: status.podIP
  extraArgs:
    config.expand-env: true

write:
  replicas: 1

read:
  replicas: 1

promtail:
  enabled: true
  config:
    snippets:
      scrapeConfigs: |
        - job_name: 'kubernetes-services'
          kubernetes_sd_configs:
            - role: endpoints
          relabel_configs:    
            - source_labels: [ __meta_kubernetes_service_name ]
              action: replace
              target_label: service
            - source_labels:
                - __meta_kubernetes_pod_controller_name
              regex: ([0-9a-z-.]+?)(-[0-9a-f]{8,10})?
              action: replace
              target_label: __tmp_controller_name
            - source_labels:
                - __meta_kubernetes_pod_label_app_kubernetes_io_name
                - __meta_kubernetes_pod_label_app
                - __tmp_controller_name
                - __meta_kubernetes_pod_name
              regex: ^;*([^;]+)(;.*)?$
              action: replace
              target_label: app
            - source_labels:
                - __meta_kubernetes_pod_label_app_kubernetes_io_instance
                - __meta_kubernetes_pod_label_release
              regex: ^;*([^;]+)(;.*)?$
              action: replace
              target_label: instance
            - source_labels:
                - __meta_kubernetes_pod_label_app_kubernetes_io_component
                - __meta_kubernetes_pod_label_component
              regex: ^;*([^;]+)(;.*)?$
              action: replace
              target_label: component
            - action: replace
              source_labels:
                - __meta_kubernetes_pod_node_name
              target_label: node_name
            - action: replace
              source_labels:
                - __meta_kubernetes_namespace
              target_label: namespace
            - action: replace
              source_labels:
                - __meta_kubernetes_pod_name
              target_label: pod
            - action: replace
              source_labels:
                - __meta_kubernetes_pod_container_name
              target_label: container
            - action: replace
              replacement: /var/log/pods/*$1/*.log
              separator: /
              source_labels:
                - __meta_kubernetes_pod_uid
                - __meta_kubernetes_pod_container_name
              target_label: __path__
            - action: replace
              regex: true/(.*)
              replacement: /var/log/pods/*$1/*.log
              separator: /
              source_labels:
                - __meta_kubernetes_pod_annotationpresent_kubernetes_io_config_hash
                - __meta_kubernetes_pod_annotation_kubernetes_io_config_hash
                - __meta_kubernetes_pod_container_name
              target_label: __path__
```
