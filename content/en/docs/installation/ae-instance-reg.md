---
title: Instance Registration
linkTitle: Registration
description: As part of the configuration process, you need to register your Armory Enterprise instance. If you maintain several instances, such as development, staging, and production instances, you must register each one.
aliases:
  - /docs/installation/deployment-reg/
  - /docs/ae-environment-reg/
---
<!--Several shortlinks point to this page from Deck:
- go.armory.io/UIdocs-deploy-reg points to the top of this page
- go.armory.io/UIdocs-deploy-reg-troubleshooting points to Troubleshooting
- go.armory.io/UIdocs-deploy-reg-manual-id points to the Operator fails to generate an instance ID section
-->

## Register your instance

1. Navigate to the URL provided by Armory and follow the instructions to create an account.
2. Make note of the following information:

   - Instance ID
   - Client ID
   - Secret
   
   **This is the only time you are shown the secret value. Store it somewhere safe.**

## Apply the registration information

In your Operator manifest (such as `spinnakerService.yml`) or `spinnaker-local` file (Halyard), configure the following parameters:
  - `spec.spinnakerConfig.profiles.spinnaker.armory.cloud.iam.tokenIssueUrl`: set this value to `https://auth.cloud.armory.io/oauth/token`.
  - `spec.spinnakerConfig.profiles.spinnaker.armory.cloud.iam.clientID`: set this value to **Client ID** that you received from [Register your instance](#register-your-instance).
  - `spec.spinnakerConfig.profiles.spinnaker.”armory.cloud”.iam.clientSecret`: set this value to the **Secret** that you received from [Register your instance](#register-your-instance).

Once you apply the registration information to your instance, your Armory Enterprise instance is registered with Armory.

{{< tabs name="Configure Armory Enterprise" >}}
{{% tab name="Operator" %}}

```yaml
spec:
  spinnakerConfig:
    profiles:
      gate:
        armory.cloud:
          enabled: true
          iam:
            tokenIssuerUrl: https://auth.cloud.armory.io/oauth/token
            clientId: <clientId>
            clientSecret: <clientSecret>
          api:
            baseUrl: https://api.cloud.armory.io
          # The following hub parameters are optional and only needed if you are using features that require Armory's hosted cloud services.
          hub:
            baseUrl: https://api.cloud.armory.io/agents
            grpc:
              host: agents.cloud.armory.io
              port: 443
              tls:
                insecureSkipVerify: true
```

Save the file and apply the manifest. This redeploys Armory Enterprise.

{{% /tab %}}

{{% tab name="Halyard" %}}

```yaml

#gate-local
armory.cloud:
  enabled: true
  iam:
    tokenIssuerUrl: https://auth.cloud.armory.io/oauth/token
    clientId: <clientId>
    clientSecret:<clientSecret>
  api:
    baseUrl: https://api.cloud.armory.io
  # The following hub parameters are optional and only needed if you are using features that require Armory's hosted cloud services.
  hub:
    baseUrl: https://api.cloud.armory.io/agents
    grpc:
      host: agents.cloud.armory.io
      port: 443
      tls:
        insecureSkipVerify: true
```

Save the file and apply the changes with the following command: `hal deploy apply`. Applying the config changes redeploys Armory Enterprise.
{{% /tab %}}
{{< /tabs >}}

## Troubleshooting

### Registration fails

This issue can occur if Operator failed to generate a UUID for your Armory Enterprise instance. You can resolve this issue with one of the following actions:

- Deploy Armory Enterprise with a supported Operator version that corresponds to your Armory Enterprise version.
- Manually apply a registration ID.

To manually apply a registration ID, perform the following steps:

1. In your Operator manifest (`spinnakerservice.yml` by default), check if there is a value for `spec.spinnakerConfig.config.armory.diagnostics.uuid`.
  
   If there is a value, use that value for the next step.

   If there is no value, generate a UUID and add it to `spec.spinnakerConfig.config.armory.diagnostics.uuid` field.

2. Add the UUID to the `spec.spinnakerConfig.profiles.spinnaker.armory.registrationId` field.
3. Save the file and deploy Armory Enterprise.

If you generated your own UUID, contact Armory Support and provide your UUID so that it can be associated with your organization. This helps with troubleshooting when you contact Support.
