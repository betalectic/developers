---
slug: how-to-run-cron-job-kubernetes
title: How to run CRON Jobs in Kubernetes
authors: [shubham]
tags: [Kubernetes, CRON]
---

For Kubernetes Version 1.20

- create a file called cronjob.yml.
- Paste the below script:

```
apiVersion: batch/v1beta1 // for kubernetes-v1.20
kind: CronJob
metadata:
  name: nemo-be-cron
spec:
  schedule: "*/1 * * * *"
  successfulJobsHistoryLimit: 0 // to delete success history
  failedJobsHistoryLimit: 1 // to keep failure history
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: nemo-be-cron
            image: CONTAINER_IMAGE
            imagePullPolicy: Always
            command: ["sh", "-c", "node ./testCommand.js"]
          restartPolicy: OnFailure
```

- Add changes to the Post script section of Buildspec.yml
