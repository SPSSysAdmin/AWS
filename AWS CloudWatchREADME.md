
# AWS CloudWatch Agent Setup Guide on EC2

## Overview
This guide walks through setting up the AWS CloudWatch Agent on an EC2 instance, configuring it via `amazon-cloudwatch-agent.json`, and running essential commands.

## Prerequisites
- **EC2 Instance** with AWS CloudWatch Agent installed.
- **Permissions**: Ensure the EC2 instance has the necessary IAM role to interact with CloudWatch.

## Steps

### 1. Edit the Configuration File

To customize which metrics to monitor, edit the `amazon-cloudwatch-agent.json` file:

```bash
sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

This configuration file allows you to specify metrics like CPU, memory, and disk usage. Example structure:

```json
{
    "metrics": {
        "namespace": "CustomNamespace",
        "append_dimensions": {
            "InstanceId": "${aws:InstanceId}"
        },
        "aggregation_dimensions": [["InstanceId"]],
        "metrics_collected": {
            "cpu": {
                "measurement": ["cpu_usage_idle"],
                "metrics_collection_interval": 60
            },
            "mem": {
                "measurement": ["mem_used_percent"],
                "metrics_collection_interval": 60
            }
        }
    }
}
```

**Note:** Adjust the settings based on your requirements.

### 2. Stop the CloudWatch Agent

Use the following command to stop the CloudWatch Agent:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a stop
```

### 3. Fetch and Apply Configuration

To fetch the updated configuration and apply it, run:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
    -a fetch-config \
    -m ec2 \
    -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

This command loads the configuration from the specified JSON file and prepares the agent for the new settings.

### 4. Start the CloudWatch Agent

Once the configuration is fetched, start the agent:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a start
```

The agent will begin collecting and sending metrics to CloudWatch based on your configuration.

---

## Verification

You can verify that the agent is running and monitoring metrics via the AWS Console in **CloudWatch > Metrics**.
