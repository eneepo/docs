---
title: "Cron Job"
description: ""
date: 2023-07-18T08:11:58+02:00
lastmod: 2023-07-18T08:11:58+02:00
draft: false
images: []
type: docs
menu:
  kubernetes:
    parent: "resources"
weight: 1040
toc: false
---

A CronJob is an API resource that allows you to schedule and automate the execution of tasks or jobs at specified time intervals. It is similar to the cron utility in Unix-like operating systems. With CronJobs, you can define a schedule for running a job or a series of jobs inside a Kubernetes cluster.

Here's an example of a CronJob manifest in Kubernetes:
```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: example-cronjob
spec:
  schedule: "*/5 * * * *"
  jobTemplate:
    completions: 3
    parallelism: 3
    spec:
      template:
        spec:
          containers:
          - name: example-container
            image: example-image
            command: ["echo", "Hello, Kubernetes!"]
          restartPolicy: OnFailure
```

In this example:

The schedule field defines the time schedule for the CronJob. The value `"*/5 * * * *"` indicates that the job should run every 5 minutes. You can use standard cron syntax to define different schedules.

The `jobTemplate` section specifies the template for creating jobs. In this case, it defines a single container named `example-container` that runs the `example-image` Docker image and executes the command `echo "Hello, Kubernetes!"`. You can modify the container specifications to run any command or application you need.

The `restartPolicy` field determines the job's restart policy. In this case, it is set to `OnFailure`, which means the job will only be restarted if it fails.

## Crontab format

```
# Minute   Hour   Day of Month   Month   Day of Week   Command   Description
# (0-59)  (0-23)     (1-31)      (1-12)     (0-6)
  
   0        0          *            *         *       backup.sh   Daily backup of files
   30       2          *            *         6       cleanup.sh  Weekly cleanup task
   0        12         *            *         1-5     report.sh   Generate daily report
   */15     *          *            *         *       monitor.sh  Run every 15 minutes
   0        0          1            *         *       maintenance.sh  Monthly maintenance task
```
In this example:

The first line is a header that describes each column in the table.
Each subsequent line represents a scheduled task with its corresponding time and description.
The first column represents the minute(s) when the task will be executed (0-59).
The second column represents the hour(s) when the task will be executed (0-23).
The third column represents the day(s) of the month when the task will be executed (1-31).
The fourth column represents the month(s) when the task will be executed (1-12).
The fifth column represents the day(s) of the week when the task will be executed (0-6, where 0 represents Sunday).
The sixth column represents the command or script that will be executed.
The last column provides a brief description of what the task does.