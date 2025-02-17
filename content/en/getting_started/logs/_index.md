---
title: Send logs to Datadog
kind: documentation
further_reading:
- link: "https://learn.datadoghq.com/enrol/index.php?id=15"
  tag: "Learning Center"
  text: "Introduction to Logs in Datadog"
- link: "/logs/log_collection/"
  tag: "Documentation"
  text: "Collect logs from your Applications, Containers, and Cloud providers"
---

## Overview

Datadog Log Management is used to collect logs from your application. This page shows you how get your first logs into Datadog. Before moving forward:

1. If you haven't already, create a [Datadog account][1] and [enable Datadog Log Management][2].
2. Set up a [Vagrant Ubuntu 16.04 virtual machine][3] using the following commands. For more information about Vagrant, see their [Getting Started][4] page:

    ```
    vagrant init ubuntu/xenial64
    vagrant up
    vagrant ssh
    ```

Once this is done, follow the sections below to discover how to:

* [Send Logs manually](#sending-logs-manually)
* [Use the Agent to send logs from a file](#send-logs-from-a-file)

## Sending logs manually

To send logs manually, use the `telnet` command with your [Datadog API key][5] within the Vagrant virtual machine.

Logs can be a full-text message:

{{< tabs >}}
{{% tab "US Site" %}}

The secure TCP endpoint is `intake.logs.datadoghq.com:10516` (or port `10514` for nonsecure connections).

```
telnet intake.logs.datadoghq.com 10514

<DATADOG_API_KEY> Plain text log sent through TCP
```

{{% /tab %}}
{{% tab "EU Site" %}}

The secure TCP endpoint is `tcp-intake.logs.datadoghq.eu:443` (or port `1883` for nonsecure connections).

```
telnet tcp-intake.logs.datadoghq.eu 1883

<DATADOG_API_KEY> Plain text log sent through TCP
```

{{% /tab %}}
{{< /tabs >}}

This produces the following result in the [Log Explorer Page][2]:

{{< img src="getting_started/logs/plain_text_log.png" alt="Custom telnet" responsive="true">}}

or a JSON object that is automatically parsed by Datadog:

{{< tabs >}}
{{% tab "US Site" %}}

```
telnet intake.logs.datadoghq.com 10514

<DATADOG_API_KEY> {"message":"JSON formatted log sent through TCP", "ddtags":"env:dev", "ddsource":"terminal", "hostname":"gs-hostame", "service":"user"}
```

{{% /tab %}}
{{% tab "EU Site" %}}

```
telnet tcp-intake.logs.datadoghq.eu 1883

<DATADOG_API_KEY> {"message":"JSON formatted log sent through TCP", "ddtags":"env:dev", "ddsource":"terminal", "hostname":"gs-hostame", "service":"user"}
```

{{% /tab %}}
{{< /tabs >}}

This produces the following result in the [Log Explorer Page][2]:

{{< img src="getting_started/logs/json_log.png" alt="JSON logs" responsive="true">}}

## Send logs from a file

### Install the Agent

To install the Datadog Agent within your Vagrant host, use the [one line install command][6] updated with your [Datadog API key][7]:

{{< tabs >}}
{{% tab "US Site" %}}

```
DD_API_KEY=<YOUR_DD_API_KEY>  bash -c "$(curl -L https://raw.githubusercontent.com/DataDog/datadog-agent/master/cmd/agent/install_script.sh)"
```

{{% /tab %}}
{{% tab "EU Site" %}}

```
DD_API_KEY=<YOUR_DD_API_KEY> DD_SITE="datadoghq.eu" bash -c "$(curl -L https://raw.githubusercontent.com/DataDog/datadog-agent/master/cmd/agent/install_script.sh)"
```

{{% /tab %}}
{{< /tabs >}}

#### Validation

Verify the Agent is running with the [status command][8] `sudo datadog-agent status`. You have not enabled log collection yet, so you should see:

```
==========
Logs Agent
==========

  Logs Agent is not running
```

**Note**: After a few minutes, you can verify that the Agent is connected to your account by checking the [Infrastructure List][9] in Datadog.

### Enable log collection

To enable log collection with the Agent, edit the `datadog.yaml` [configuration file][10] located at `/etc/datadog-agent/datadog.yaml` and set `logs_enabled:true`:

```yaml
## @param logs_enabled - boolean - optional - default: false
## Enable Datadog Agent log collection by setting logs_enabled to true.
#
logs_enabled: true
```

### Monitor a custom file
#### Create the log file

In order to collect logs from a custom file, first create the file and add one line of logs to it:

```
$ touch log_file_to_monitor.log

$ echo "First line of log" >> log_file_to_monitor.log
```

#### Configure the Agent

To specify to the Agent to monitor this log file:

1. Create a new configuration folder in the [configuration directory of the Agent][11]:

    ```
    sudo mkdir /etc/datadog-agent/conf.d/custom_log_collection.d/
    ```

2. Create your configuration file within this new configuration folder:

    ```
    sudo touch /etc/datadog-agent/conf.d/custom_log_collection.d/conf.yaml
    ```

3. Copy and paste the following content within this `conf.yaml` file:

      ```
      logs:
        - type: file
          path: /home/ubuntu/log_file_to_monitor.log
          source: custom
          service: user
      ```

4. Restart the Agent: `sudo service datadog-agent restart`

##### Validation

If the log configuration is correct, the [status command][8] `sudo datadog-agent status` outputs:

```
==========
Logs Agent
==========
    LogsProcessed: 0
    LogsSent: 0

  custom_log_collection
  ---------------------
    Type: file
    Path: /home/ubuntu/log_file_to_monitor.log
    Status: OK
    Inputs: /home/ubuntu/log_file_to_monitor.log
```

### Add new logs to the file

Now that everything is configured correctly, add new entries to your log file to see them within Datadog:

```
$ echo "New line of log in the log file" >> log_file_to_monitor.log
```

This produces the following result in the [Log Explorer Page][2]:

{{< img src="getting_started/logs/file_log_example.png" alt="File log example" responsive="true">}}

## Further reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://www.datadoghq.com
[2]: https://app.datadoghq.com/logs
[3]: https://app.vagrantup.com/ubuntu/boxes/xenial64
[4]: https://www.vagrantup.com/intro/getting-started/index.html
[5]: https://app.datadoghq.com/account/settings#api
[6]: https://app.datadoghq.com/account/settings#agent/ubuntu
[7]: https://app.datadoghq.com/account/settings#api
[8]: /agent/guide/agent-commands/?tab=agentv6#agent-information
[9]: https://app.datadoghq.com/infrastructure
[10]: /agent/guide/agent-configuration-files/?tab=agentv6#agent-main-configuration-file
[11]: /agent/guide/agent-configuration-files/?tab=agentv6#agent-configuration-directory
