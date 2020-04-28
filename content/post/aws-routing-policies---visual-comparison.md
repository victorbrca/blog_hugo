---
title: "AWS Routing Policies - Visual Comparison"
date: 2020-04-28T14:45:00-04:00
draft: false
author: "Victor Mendonça"
description: "A visual overview of the different AWS route 53 routing policies"
tags: ["AWS"]
---

A quick explanation and visual overview of the AWS Route 53 policies (with the exception of Geoproximity Routing).

### Simple Routing Policy

Use for a single resource that performs a given function for your domain, for example, a web server that serves content for the example.com website.

**Important points to remember:**

+ Simplest routing policy
+ Only one DNS record set
+ Multiple IP address per record set can be used
+ Values are returned to user in random order
+ No health checks

![](img/aws-routing-policies-visual-comparison/simple_routing.png)

### Weighted Routing Policy

Weighted Routing Policy controls the percentage of the requests that go to a specific endpoint.

**Important points to remember:**

+ Weighted routing sends user traffic based on the weight that you supply
+ You can split traffic between different regions
+ Multiple IP address per record set can be used
+ Health checks can be used

![](img/aws-routing-policies-visual-comparison/weighted_routing.png)

### Latency Routing Policy

Use when you have resources in multiple AWS Regions and you want to route traffic to the region that provides the best latency.

**Important points to remember:**

+ Routing will be based on user to region latency
+ Multiple IP address per record set can be used
+ Health checks can be used

![](img/aws-routing-policies-visual-comparison/latency_routing.png)

### Failover Routing Policy

Use failover routing policy when you want to configure active-passive failover.

**Important points to remember:**

+ Use failover routing policy when you want to configure active-passive failover
+ Health checks
  - You can't save the primary record without a health check
  - The secondary record can be created without a health check

![](img/aws-routing-policies-visual-comparison/failover_routing.png)

### Geolocation Routing Policy

Geolocation routing lets you choose the resources that serve your traffic based on the geographic location of your users, meaning the location that DNS queries originate from.

**Important points to remember:**

+ This is routing based on user's location
+ Multiple IP address per record set can be used
+ Health checks can be used

![](img/aws-routing-policies-visual-comparison/geolocation_routing.png)

### Multivalue Answer Routing policy

Use when you want Route 53 to respond to DNS queries with up to eight healthy records selected at random.

**Important points to remember:**

+ It's very similar to simple routing, but with two differences:
  + You can have multiple record sets
  + You can have health checks

![](img/aws-routing-policies-visual-comparison/multivalue_routing.png)
