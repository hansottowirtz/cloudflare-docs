---
title: Policy design
pcx_content_type: overview
weight: 1
layout: learning-unit
---

Most policy building for private network access happens within the Gateway DNS and Gateway Network policy builders. For the most part, customers use a mixture of DNS resolution, SNI hostname values, and IP address groupings as the baseline for defining policies that pertain to specific applications.

## Considerations

Before building your policies, it is helpful to ask yourself a few questions:

- Should all users and services be able to reach all connected subnets? Are there explicit exceptions?
- Do all applications live within a primary network range, and are they defined by static or dynamic hosts and IP addresses?
- Are there DevOps workflows that rely on completely ephemeral IPs or subdomains?
- Do you have sources of truth for identity and device posture that will be used in policies?
- Do you plan to immediately implement a default-deny model? In other words, will you block all users except for those who match an explicit Allow policy?

## Prepare to build policies

We recommend approaching Zero Trust Network Access in three distinct steps to guide policy building.

### 1. Determine your sources of truth

#### Identity

Determine which identity provider you will use as the source of truth for [identity-based attributes](/cloudflare-one/policies/gateway/identity-selectors/) such as user email and user groups.

{{<Aside type="note">}}
Ensure that the [identity provider is connected to Cloudflare](/learning-paths/replace-vpn/get-started/configure-idp/) and available to users in your [device enrollment permissions](/learning-paths/replace-vpn/configure-device-agent/device-enrollment-permissions/).
{{</Aside>}}

If you plan to grant access to services based on group membership, verify that the groups are being passed from your identity provider to Gateway:

1. In [Zero Trust](https://one.dash.cloudflare.com/), go to **My Team** > **Users**. This page lists the users who have authenticated to the WARP client.
2. Select a user.
3. Under **User Registry identity**, select the user name.

You will see the identity that Gateway will use when evaluating policies.

#### Device posture

Most customers will also build policies that are contingent on the use of a corporate device. For example, all users on corporate devices can access `*.jira.internal.com`, but users on personal devices can only access `dev.internal.jira.com`. In order for this to be effective, we recommend defining a source of truth for your corporate devices. This is sometimes the presence of a specific [issued certificate](/cloudflare-one/identity/devices/warp-client-checks/client-certificate/), the presence of a [process with a matched hash](/cloudflare-one/identity/devices/warp-client-checks/application-check/), or an API integration with a supported [thirty-party endpoint security provider](/cloudflare-one/identity/devices/service-providers/) like Crowdstrike or SentinelOne.

{{<Aside type="note">}}
Be sure to [enable the device posture checks](/cloudflare-one/identity/devices/) that you want to use in your policies.
{{</Aside>}}

### 2. Define your networks

Almost all businesses have a series of interconnected networks, either physical or virtual. Prepare a list of all relevant networks, subnets, or segments within your network that users currently access, either locally or when using the VPN. For example,

| Network name | Location | IP range | Accessible by VPN? |
|- | - | - |-|

### 3. Define your applications

Next, prepare a list of all relevant internal applications on your networks that will have distinct policy requirements (for example, different user identity or device posture requirements). Each application should be defined by an IP list, a hostname/domain list, or sometimes both.

| Application name | Local IPs   | Hostnames | Accessible via IP? | Static or dynamic IP? |
| ---------------- | ---------- | ----------| -------- | ------------------|
| Company Wiki     |            | `wiki.internal.com`   |   Yes      | Static |

For example, you may have an application at `a.internal.com` which points to a load balancer with a static IP address, balancing a series of dynamic hosts serving the application on `a.internal.com`. Because the IPs of the application hosts are dynamic, the best practice would be to build two policies: a network policy for the load balancer IP, and a DNS policy for the application hostnames.

On the other hand, if the IPs behind the load balancer are static or only semi-dynamic, it may make sense to directly use the application IPs in your network policy. You can build a workflow to update the application IP list via a Cloudflare API call whenever host changes are made in your infrastructure provider.