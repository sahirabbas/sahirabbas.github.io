# Automated Cleanup of Evicted Pods in Kubernetes (Workaround)
---

## Introduction

Pod eviction can occur for various reasons, presenting challenges to Kubernetes administrators. Examples include scaling by VerticalPodAutoscaler, resource pressure on a Node, higher priority Pod preemption, or exceeding an emptyDir usage limit. However, Kubernetes does not automatically remove evicted pods. In this article, we introduce a practical workaround utilizing cronjobs to automate the cleanup process for evicted pods.

## Prerequisites

* Kubernetes Cluster admin rights.
* Helm installed.

## Workaround Deployment

We have developed a convenient Helm template for deploying the cronjob to handle evicted pod cleanup effectively. The chart sets up the following Kubernetes resources:

1. Namespace
2. Service account
3. Cluster Role
4. Cluster Rolebinding
5. Cronjob

## Step-by-Step Deployment

1. Clone the GitHub repository containing the Helm chart:

```
git clone https://github.com/sahirabbas/remove_evicted_pods_workaround.git
```
2. Deploy the cronjob using Helm in your Kubernetes Cluster:

```
cd remove_evicted_pods_workaround 
helm install <name> .
```

Note: Replace `<name>` with a suitable identifier for your deployment.

By default, the chart will create a namespace called evicted-pods-cleanup, and the cronjob will run every 30 minutes. However, you can override these values by modifying the Helm chart with your desired settings.

## Overridable Values

| Parameter      | Description                         |   Default Value |
| ----------- | -------------------------------------- |-----------------|
| `namespace`       | Namespace for the resources      |`evicted-pods-cleanup` |
| `cronjob.name`       | Name of the cronjob |`evicted-pods-cleanup`
| `cronjob.schedule`    | Cron expression for the job execution | `*/30 * * * *` |
| `serviceaccount.name` | ServiceAccount Name | `podevict-sa` |
|`clusterrole.name` |   	ClusterRole Name| `romove-evicted` |
| `clusterrolebinding.name` |	ClusterRoleBinding Name |	`romove-evicted` |
| `cronjob.activedeadlineseconds`	| activeDeadlineSeconds for the Cronjob	| `1500` |
| `cronjob.startingdeadlineseconds`	| startingDeadlineSeconds for the Cronjob |	`180` |
| `failedjobshistorylimit`	| failedJobsHistoryLimit to keep	| `1` |
| `successfuljobshistorylimit` | successfulJobsHistoryLimit to keep	| `1` |

To override a value, like the cron timing, use the following command:

```
helm install remove-evic --set cronjob.schedule='*/20 * * * *' .
```

With this automated cleanup solution in place, you can rest assured that evicted pods will be efficiently managed, contributing to the smooth operation of your Kubernetes Cluster. This workaround empowers administrators to focus on higher-level tasks while keeping the environment clean and optimized.

---