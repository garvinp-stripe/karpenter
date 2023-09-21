---
title: "Cluster Rightsizing"
linkTitle: "Cluster Rightsizing"
weight: 8
description: >
  How Karpenter right size the cluster
---

## Overview
Karpenter right size the cluster in two main ways. First during provisioning and determing the right instance type to launch and second consolidating nodes.

## Provisioning
During [provisioning](./scheduling.md), Karpenter evaluates all pending pods within the [batchMaxDuration](./settings.md/#batchmaxduration) for the resources they requested. Once Karpenter determines which provisioner will handle which subset of pending pods or all pending pods, it will then find the lowest cost instance type that is able to satisfy the pending pod's requests. In addition, it would evaluate other aspect of a pod's need such as topology spread and affinity using its internal scheduler. Once it determines how many nodes and what type nodes are needed, it will launch these into the cluster.

## Consolidation
During [consolidation](https://github.com/aws/karpenter/blob/main/designs/consolidation.md), Karpenter evaluates if it can remove nodes or replace nodes with a lower cost one while still be able to fit all running pods. The linked document goes more in detail in what Karpenter considers during consolidation.

## Behavior
Karpenter managed cluster instance type distribution is highly variable depending on:
- How often and how big/ many pods are in the unschedulable state
- Min and max instance types in provisioners
- How stable the resource requirements are in the cluster
- How many interacting scheduling constraints there are between pods within the cluster


### Small node bias clusters
Karpenter is more cautious when it comes to consolidation to maximize safety, consolidation from multiple nodes into bigger nodes tends to happen less or takes longer to occur. In addition, Karpenter's provisioning path is simplier and does not consider reconfigurating existing nodes to consolidate nodes. 
- If over the course of a period of time a Karpenter managed cluster tends to increase node count in small increments, the cluster would tend to have a higher mixture of smaller instance types.
- If a Karpenter managed cluster has a lot of pods with scheduling constraints, it would also tends to introduce more spread hence higher mix of smaller instance types
- 
