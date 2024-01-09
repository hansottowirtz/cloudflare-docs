---
title: Define device enrollment permissions
pcx_content_type: overview
weight: 1
layout: learning-unit
---

Device enrollment permissions determine which users can connect new devices to your organization's Cloudflare Zero Trust instance. Once the user registers their device, the WARP client will store their identity token and use it to authenticate to services in your private network.

## Set device enrollment permissions

{{<render file="warp/_device-enrollment.md" productFolder="cloudflare-one">}}

## Only allow corporate devices

Device posture evaluation happens after a device has already enrolled in your Zero Trust organization. If you want only specific devices to be able to enroll, we recommend adding a [mutual TLS authentication](/cloudflare-one/identity/devices/access-integrations/mutual-tls-authentication/) rule to your device enrollment policy. This rule will check for the presence of a specific client certificate on the enrolling devices.

{{<Aside type="note">}}
Mutual TLS authentication is only available on Enterprise plans.
{{</Aside>}}

{{<render file="warp/_device-enrollment-mtls.md" productFolder="cloudflare-one">}}

## Best practices

????????

Use a single source of truth identity for your corporate users, for example by requiring company email addresses to login with your primary identity provider. Only allow other login methods or identity providers for contractors, vendors, or acquired corporations who may need access to your network.