# aws-datadog-infra

This repository contains AWS CloudFormation stacks and stacksets to automate the setup of observability infrastructure across an AWS Organization, with a focus on Datadog integration.

## Features

- **Datadog Metrics and Logging Integration**: Deploy CloudFormation templates to configure Datadog metrics and logging in multiple AWS accounts.
- **Centralized Observability Account**: Set up a central AWS account to aggregate all metrics and logs from member accounts and forward them to Datadog.
- **StackSets for Organization-wide Deployment**: Use CloudFormation StackSets to deploy observability resources across all or selected AWS accounts in your organization.
- **Automated Resource Provisioning**: Templates include IAM roles, log forwarding, metric streams, and necessary permissions for seamless Datadog integration.

## Repository Structure

- `stacksets/` — CloudFormation StackSet templates for organization-wide deployment.

## Architecture Overview

This solution deploys a set of AWS resources across your AWS Organization to enable centralized observability and Datadog integration:

- **Member Accounts**:
  - Deploy IAM roles and policies to allow log and metric forwarding.
  - Set up CloudWatch Log subscription filters and Kinesis Firehose streams to send logs to a central account or directly to Datadog.
  - Create CloudWatch Metric Streams to forward metrics to a central account or Datadog.
  - Establish OAM (Observability Access Manager) Links to a central OAM Sink for cross-account metric sharing.

- **Central Observability Account**:
  - Hosts the OAM Sink, which receives metrics from member accounts via OAM Links.
  - Aggregates logs and metrics, and forwards them to Datadog using Kinesis Firehose and Metric Streams.
  - Manages IAM roles and policies for secure cross-account access.

## Template Relationships

- **`stacksets/datadog-integration/stackset.yaml`**: Deploys Datadog's official integration stackset to all target accounts for basic Datadog setup.
- **`stacksets/logging/stackset.yaml`** and **`stacksets/logging/template.yaml`**: Deploy log shipping resources (IAM roles, log policies, Firehose) to member accounts for forwarding logs to the central account or Datadog.
- **`stacksets/metrics/stackset.yaml`**, **`stacksets/metrics/oam-link-template.yaml`**, and **`stacksets/metrics/cw-cross-account-sharing-template.yaml`**: Deploy OAM Links and cross-account sharing roles to enable metric sharing from member accounts to the central account.
- **`stacksets/datadog-shipping/stackset.yaml`** and related templates: Deploy resources in the central account to receive logs/metrics and forward them to Datadog.
- **Root-level templates** (e.g., `template.yaml`, `stacksets-shipping-template.yaml`): Compose and orchestrate the deployment of the above stacksets and templates for a full organization-wide rollout.

## Datadog template Updates

Due to a limitation in AWS SAM's `package` command in `template.yaml` the `DatadogIntegrationStack` `Location` parameter must be kept in sync manually with the `DatadogTemplateUrl` value.