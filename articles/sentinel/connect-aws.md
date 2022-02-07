---
title: Connect Microsoft Sentinel to Amazon Web Services to ingest AWS service log data
description: Use the AWS connector to delegate Microsoft Sentinel access to AWS resource logs, creating a trust relationship between Amazon Web Services and Microsoft Sentinel.
author: yelevin
ms.topic: how-to
ms.date: 11/18/2021
ms.author: yelevin

---

# Connect Microsoft Sentinel to Amazon Web Services to ingest AWS service log data

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Use the Amazon Web Services (AWS) connectors to pull AWS service logs into Microsoft Sentinel. These connectors work by granting Microsoft Sentinel access to your AWS resource logs. Setting up the connector establishes a trust relationship between Amazon Web Services and Microsoft Sentinel. This is accomplished on AWS by creating a role that gives permission to Microsoft Sentinel to access your AWS logs.

This connector is available in two versions: the legacy connector for CloudTrail management and data logs, and the new version that can ingest logs from the following AWS services by pulling them from an S3 bucket:

- [Amazon Virtual Private Cloud (VPC)](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) - [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
- [Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html) - [Findings](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html)
- [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) - [Management](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-management-events-with-cloudtrail.html) and [data](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) events

> [!IMPORTANT]
>
> - The Amazon Web Services S3 connector is currently in **PREVIEW**. See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

# [S3 connector (new)](#tab/s3)

This document explains how to configure the new AWS S3 connector. The process of setting it up has two parts: the AWS side and the Microsoft Sentinel side.

1. In your AWS environment:

    - Configure your AWS service(s) to send logs to an **S3 bucket**.

    - Create a **Simple Queue Service (SQS) queue** to provide notification.

    - Create an **assumed role** to grant permissions to your Microsoft Sentinel account (external ID) to access your AWS resources.

    - Attach the appropriate **IAM permissions policies** to grant Microsoft Sentinel access to the appropriate resources (S3 bucket, SQS).

1. In Microsoft Sentinel:

    - Enable and configure the **AWS S3 Connector** in the Microsoft Sentinel portal. See the instructions below.

Each side's process produces information used by the other side. This sharing creates secure communication.

We have made available, in our GitHub repository, a script that **automates the AWS side of this process**. See the instructions for [automatic setup](#automatic-setup) later in this document.

## Architecture overview

This graphic and the following text shows how the parts of this connector solution interact.

:::image type="content" source="media/connect-aws/s3-connector-architecture.png" alt-text="Screenshot of A W S S 3 connector architecture.":::

- AWS services are configured to send their logs to S3 (Simple Storage Service) storage buckets.

- The S3 bucket sends notification messages to the SQS (Simple Queue Service) message queue whenever it receives new logs.

- The Microsoft Sentinel AWS S3 connector polls the SQS queue at regular, frequent intervals. If there is a message in the queue, it will contain the path to the log files.

- The connector reads the message with the path, then fetches the files from the S3 bucket.

- To connect to the SQS queue and the S3 bucket, Microsoft Sentinel uses AWS credentials and connection information embedded in the AWS S3 connector's configuration. The AWS credentials are configured with a role and a permissions policy giving them access to those resources. Similarly, the Microsoft Sentinel workspace ID is embedded in the AWS configuration, so there is in effect two-way authentication.

## Global prerequisites

You must have write permission on your Microsoft Sentinel workspace.

## Automatic setup

To simplify the onboarding process, Microsoft Sentinel has provided a [PowerShell script to automate the setup](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/AWS-S3) of the AWS side of the connector - the required AWS resources, credentials, and permissions.

The script takes the following actions:

- Creates an *IAM assumed role* with the minimal necessary permissions, to grant Microsoft Sentinel access to your logs in a given S3 bucket and SQS queue.

- Enables specified AWS services to send logs to that S3 bucket, and notification messages to that SQS queue.

- If necessary, creates that S3 bucket and that SQS queue for this purpose.

- Configures any necessary IAM permissions policies and applies them to the IAM role created above.

### Prerequisites

You must have PowerShell and the AWS CLI on your machine.

   - [Installation instructions for PowerShell](/powershell/scripting/install/installing-powershell)
   - [Installation instructions for the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

### Instructions

To run the script to set up the connector, use the following steps:

1. From the Microsoft Sentinel navigation menu, select **Data connectors**.

1. Select **Amazon Web Services S3** from the data connectors gallery, and in the details pane, select **Open connector page**.

1. In the **Configuration** section, under **1. Set up your AWS environment**, expand **Setup with PowerShell script (recommended)**.

1. Follow the on-screen instructions to download and extract the [AWS S3 Setup Script](https://github.com/Azure/Azure-Sentinel/blob/master/DataConnectors/AWS-S3/ConfigAwsS3DataConnectorScripts.zip?raw=true) (link downloads a zip file containing the main setup script and helper scripts) from the connector page.

1. Before running the script, run the `aws configure` command from your PowerShell command line, and enter the relevant information as prompted. See [AWS Command Line Interface | Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) for details.

1. Now run the script. Copy the command from the connector page (under "Run script to set up the environment") and paste it in your command line.

1. The script will prompt you to enter your Workspace ID. This ID appears on the connector page. Copy it and paste it at the prompt of the script.

   :::image type="content" source="media/connect-aws/aws-run-script.png" alt-text="Screenshot of command to run setup script and workspace I D." lightbox="media/connect-aws/aws-run-script.png":::

1. When the script finishes running, copy the **Role ARN** and the **SQS URL** from the script's output (see example in first screenshot below) and paste them in their respective fields in the connector page under **2. Add connection** (see second screenshot below).

   :::image type="content" source="media/connect-aws/aws-script-output.png" alt-text="Screenshot of output of A W S connector setup script." lightbox="media/connect-aws/aws-script-output.png":::

   :::image type="content" source="media/connect-aws/aws-add-connection-auto.png" alt-text="Screenshot of pasting the A W S role information from the script, to the S3 connector." lightbox="media/connect-aws/aws-add-connection-auto.png":::

1. Select a data type from the **Destination table** drop-down list. This tells the connector which AWS service's logs this connection is being established to collect, and into which Log Analytics table it will store the ingested data. Then select **Add connection**.

> [!NOTE]
> The script may take up to 30 minutes to finish running.

## Manual setup

Microsoft recommends using the automatic setup script to deploy this connector. If for whatever reason you do not want to take advantage of this convenience, follow the steps below to set up the connector manually.

### Prerequisites

- You must have an **S3 bucket** to which you will ship the logs from your AWS services - VPC, GuardDuty, or CloudTrail.

    - Create an [S3 storage bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html) in AWS.

- You must have an **SQS message queue** to which the S3 bucket will publish notifications.

    - Create a [standard Simple Queue Service (SQS) queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-create-queue.html) in AWS.

### Instructions

The manual setup consists of the following steps:
- [Create an AWS assumed role and grant access to the AWS Sentinel account](#create-an-aws-assumed-role-and-grant-access-to-the-aws-sentinel-account)
- [Configure an AWS service to export logs to an S3 bucket](#configure-an-aws-service-to-export-logs-to-an-s3-bucket)
- [Create a Simple Queue Service (SQS) in AWS](#create-a-simple-queue-service-sqs-in-aws)
- [Enable SQS notification](#enable-sqs-notification)
- [Apply IAM permissions policies](#apply-iam-permissions-policies)

#### Create an AWS assumed role and grant access to the AWS Sentinel account

1. In Microsoft Sentinel, select **Data connectors** and then select the **Amazon Web Services S3** line in the table and in the AWS pane to the right,  select **Open connector page**.

1. Under **Configuration**, copy the **External ID (Workspace ID)** and paste it aside.
 
1. In your AWS management console, under **Security, Identity & Compliance**, select **IAM**.

   :::image type="content" source="media/connect-aws/aws-select-iam.png" alt-text="Screenshot of Amazon Web Services console.":::

1. Choose **Roles** and select **Create role**.

   :::image type="content" source="media/connect-aws/aws-select-roles.png" alt-text="Screenshot of A W S roles creation screen.":::

1. Choose **Another AWS account.** In the **Account ID** field, enter the number **197857026523** (you can copy and paste it from here). This number is **Microsoft Sentinel's service account ID for AWS**. It tells AWS that the account using this role is a Microsoft Sentinel user.

   :::image type="content" source="media/connect-aws/aws-enter-account.png" alt-text="Screenshot of A W S role configuration screen.":::

1. Select the **Require External ID** check box, and then enter the **External ID (Workspace ID)** that you copied from the AWS connector page in the Microsoft Sentinel portal and pasted aside. Then select **Next: Permissions**.

   :::image type="content" source="media/connect-aws/aws-enter-external-id.png" alt-text="Screenshot of continuation of A W S role configuration screen.":::

1. Skip the next step, **Attach permissions policies**, for now. You'll come back to it later [when instructed](#apply-iam-permissions-policies). Select **Next: Tags**.

   :::image type="content" source="media/connect-aws/aws-skip-permissions.png" alt-text="Screenshot of Next: Tags.":::

1. Enter a **Tag** (optional). Then select **Next: Review**.

   :::image type="content" source="media/connect-aws/aws-add-tags.png" alt-text="Screenshot of tags screen.":::

1. Enter a **Role name** and select **Create role**.

   :::image type="content" source="media/connect-aws/aws-create-role.png" alt-text="Screenshot of role naming screen.":::

1. In the **Roles** list, select the new role you created.

   :::image type="content" source="media/connect-aws/aws-select-role.png" alt-text="Screenshot of roles list screen.":::

1. Copy the **Role ARN** and paste it aside.

   :::image type="content" source="media/connect-aws/aws-copy-role-arn.png" alt-text="Screenshot of copying role A R N.":::

1. In the AWS SQS dashboard, select the SQS queue you created, and copy the URL of the queue.

    :::image type="content" source="media/connect-aws/aws-copy-queue-url.png" alt-text="Screenshot of copying S Q S queue U R L.":::

1. In the AWS S3 connector page in the Microsoft Sentinel portal, under **2. Add connection**:
    1. Paste the IAM role ARN you copied two steps ago into the **Role ARN** field.
    1. Paste the URL of the SQS queue you copied in the last step into the **SQS URL** field.
    1. Select a data type from the **Destination table** drop-down list. This tells the connector which AWS service's logs this connection is being established to collect, and into which Log Analytics table it will store the ingested data.
    1. Select **Add connection**.

   :::image type="content" source="media/connect-aws/aws-add-connection.png" alt-text="Screenshot of adding an A W S role connection to the S3 connector." lightbox="media/connect-aws/aws-add-connection.png":::

#### Configure an AWS service to export logs to an S3 bucket

- [Publish a VPC flow log to an S3 bucket](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-s3.html).

    > [!NOTE]
    > If you choose to customize the log's format, you must include the *start* attribute, as it maps to the *TimeGenerated* field in the Log Analytics workspace. Otherwise, the *TimeGenerated* field will be populated with the event's *ingested time*, which doesn't accurately describe the log event.

- [Export your GuardDuty findings to an S3 bucket](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_exportfindings.html).

    > [!NOTE]
    > The *TimeGenerated* field is populated with the finding's *Update at* value.

- AWS CloudTrail trails are stored in S3 buckets by default.
    - [Create a trail for a single account](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html).
    - [Create a trail spanning multiple accounts across an organization](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-trail-organization.html).

#### Create a Simple Queue Service (SQS) in AWS

If you haven't yet [created an SQS queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-create-queue.html), do so now.

#### Enable SQS notification

[Configure your S3 bucket to send notifications to your SQS queue](https://docs.aws.amazon.com/AmazonS3/latest/userguide/enable-event-notifications.html).

#### Apply IAM permissions policies

Permissions policies that must be applied to the [Microsoft Sentinel role you created](#create-an-aws-assumed-role-and-grant-access-to-the-aws-sentinel-account) include the following:

- AmazonSQSReadOnlyAccess
- AWSLambdaSQSQueueExecutionRole
- AmazonS3ReadOnlyAccess
- KMS access

   For information on these and additional policies that should be applied for ingesting the different types of AWS service logs, see the [AWS S3 connector permissions policies page](https://github.com/Azure/Azure-Sentinel/blob/master/DataConnectors/AWS-S3/AwsRequiredPolicies.md) in our GitHub repo.

## Known issues and troubleshooting

### Known issues

- Different types of logs can be stored in the same S3 bucket, but should not be stored in the same path.

- Each SQS queue should point to one type of message, so if you want to ingest GuardDuty findings *and* VPC flow logs, you should set up separate queues for each type.

- Similarly, a single SQS queue can serve only one path in an S3 bucket, so if for any reason you are storing logs in multiple paths, each path requires its own dedicated SQS queue.

### Troubleshooting steps

1. **Verify that log data exists in your S3 bucket.**

   View the S3 bucket dashboard and verify that data is flowing to it. If not, check that you have set up the AWS service correctly.

1. **Verify that messages are arriving in the SQS queue.**

   View the AWS SQS queue dashboard - under the Monitoring tab, you should see traffic in the "Number Of Messages Sent" graph widget. If you see no traffic, check that S3 PUT object notification is configured correctly. 

1. **Verify that messages are being read from the SQS queue.**

   Check the "Number of Messages Received" and "Number of Messages Deleted" widgets in the queue dashboard. If there are no notifications under messages deleted," then check health messages. It's possible that some permissions are missing. Check your IAM configurations. 

For more information, see [Monitor the health of your data connectors](monitor-data-connector-health.md).


# [CloudTrail connector (legacy)](#tab/ct)

> [!NOTE]
> AWS CloudTrail has [built-in limitations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) in its LookupEvents API. It allows no more than two transactions per second (TPS) per account, and each query can return a maximum of 50 records. Consequently, if a single tenant constantly generates more than 100 records per second in one region, backlogs and delays in data ingestion will result.
> Currently, you can only connect your AWS Commercial CloudTrail to Microsoft Sentinel and not AWS GovCloud CloudTrail.

## Prerequisites

You must have write permission on the Microsoft Sentinel workspace.

> [!NOTE]
> Microsoft Sentinel collects CloudTrail management events from all regions. It is recommended that you do not stream events from one region to another.

## Connect AWS CloudTrail

1. In Microsoft Sentinel, select **Data connectors** and then select the **Amazon Web Services** line in the table and in the AWS pane to the right,  select **Open connector page**.

1. Follow the instructions under **Configuration** using the following steps.
 
1.  In your Amazon Web Services console, under **Security, Identity & Compliance**, select **IAM**.

    :::image type="content" source="media/connect-aws/aws-select-iam.png" alt-text="Screenshot of Amazon Web Services console.":::

1.  Choose **Roles** and select **Create role**.

    :::image type="content" source="media/connect-aws/aws-select-roles.png" alt-text="Screenshot of A W S roles creation screen.":::

1. Choose **Another AWS account.** In the **Account ID** field, enter the number **197857026523** (you can copy and paste it from here). This number is **Microsoft Sentinel's service account ID for AWS**. It tells AWS that the account using this role is a Microsoft Sentinel user.

   :::image type="content" source="media/connect-aws/aws-enter-account.png" alt-text="Screenshot of A W S role configuration screen.":::

1. Select the **Require External ID** check box, and then enter the **External ID (Workspace ID)** that can be found in the AWS connector page in Microsoft Sentinel. This identifies *your specific Microsoft Sentinel account* to AWS. Then select **Next: Permissions**.

   :::image type="content" source="media/connect-aws/aws-enter-external-id.png" alt-text="Screenshot of continuation of A W S role configuration screen.":::

1.  Under **Attach permissions policies**, mark the check box next to **AWSCloudTrailReadOnlyAccess**. Then select **Next: Tags**.

    :::image type="content" source="media/connect-aws/aws-apply-permissions.png" alt-text="Screenshot of A W S apply permissions screen.":::

1. Enter a **Tag** (optional). Then select **Next: Review**.

   :::image type="content" source="media/connect-aws/aws-add-tags.png" alt-text="Screenshot of tags screen.":::

1. Enter a **Role name** and select **Create role**.

   :::image type="content" source="media/connect-aws/aws-create-role-ct.png" alt-text="Screenshot of role naming screen.":::

1. In the **Roles** list, select the new role you created.

   :::image type="content" source="media/connect-aws/aws-select-role.png" alt-text="Screenshot of roles list screen.":::

1. Copy the **Role ARN** and paste it aside.

   :::image type="content" source="media/connect-aws/aws-copy-role-arn.png" alt-text="Screenshot of copying role A R N.":::

1.  In the Amazon Web Services connector page in Microsoft Sentinel, paste the **Role ARN** into the **Role to add** field and select **Add**.

    :::image type="content" source="media/connect-aws/aws-add-connection-ct.png" alt-text="Screenshot of adding an A W S role connection to the AWS connector." lightbox="media/connect-aws/aws-add-connection-ct.png":::

1. To use the relevant schema in Log Analytics for AWS events, search for **AWSCloudTrail**.

    > [!IMPORTANT]
    > As of December 1, 2020, the **AwsRequestId** field has been replaced by the **AwsRequestId_** field (note the added underscore). The data in the old **AwsRequestId** field will be preserved through the end of the customer's specified data retention period.

---

## Next steps

In this document, you learned how to connect to AWS resources to ingest their logs into Microsoft Sentinel. To learn more about Microsoft Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](get-visibility.md).
- Get started [detecting threats with Microsoft Sentinel](detect-threats-built-in.md).
- [Use workbooks](monitor-your-data.md) to monitor your data.
