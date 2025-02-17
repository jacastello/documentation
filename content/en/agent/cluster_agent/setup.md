---
title: Cluster Agent Setup
kind: documentation
further_reading:
- link: "https://www.datadoghq.com/blog/datadog-cluster-agent/"
  tag: "Blog"
  text: "Introducing the Datadog Cluster Agent"
- link: "https://www.datadoghq.com/blog/autoscale-kubernetes-datadog/"
  tag: "Blog"
  text: "Autoscale your Kubernetes workloads with any Datadog metric"
- link: "/agent/autodiscovery/clusterchecks"
  tag: "Documentation"
  text: "Running Cluster Checks with Autodiscovery"
- link: "agent/kubernetes/daemonset_setup"
  tag: "Documentation"
  text: "Kubernetes DaemonSet Setup"
- link: "/agent/cluster_agent/troubleshooting"
  tag: "Documentation"
  text: "Troubleshooting the Datadog Cluster Agent"
---

To set up the Datadog Cluster Agent on your Kubernetes cluster, follow these steps:

1. [Set up the Datadog Cluster Agent](#configure-the-datadog-cluster-agent).
2. [Configure your Agent to communicate with the Datadog Cluster Agent](#configure-the-datadog-agent)

## Configure the Datadog Cluster Agent
### Step 1 - Configure RBAC permissions

The Datadog Cluster Agent needs a proper RBAC to be up and running:

1. Review the manifests in the [Datadog Cluster Agent RBAC folder][1]. Note that when using the Cluster Agent, your node Agents are not able to interact with the Kubernetes API server—only the Cluster Agent is able to do so.

2. To configure Cluster Agent RBAC permissions, apply the following manifests:

  ```
  kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/clusterrole.yaml"
  kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/serviceaccount.yaml"
  kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/clusterrolebinding.yaml"
  ```

  This creates the appropriate `ServiceAccount`, `ClusterRole`, and `ClusterRoleBinding` for the Cluster Agent.

### Step 2 - Secure Cluster-Agent-to-Agent Communication

Use one of the following options to secure communication between the Datadog Agent and the Datadog Cluster Agent.

* Create a secret and access it with an environment variable.
* Set a token in an environment variable.
* Use a ConfigMap to manage your secrets.

Setting the value without a secret results in the token being readable in the `PodSpec`.

{{< tabs >}}
{{% tab "Secret" %}}

1. Run the following command to create a secret token:

    ```
    echo -n '<ThirtyX2XcharactersXlongXtoken>' | base64
    ```

2. Run this one line command:

    ```
    kubectl create secret generic datadog-auth-token --from-literal=token=<ThirtyX2XcharactersXlongXtoken>
    ```

    Alternatively, you can specify the token in the `dca-secret.yaml` file located in the [`manifest/cluster-agent` directory][1].

3. Refer to this secret with the environment variable `DD_CLUSTER_AGENT_AUTH_TOKEN` in the manifests of the Cluster Agent. See [Step 3 - Create the Cluster Agent and its service](#step-3-create-the-cluster-agent-and-its-service)) and [Step 2 - Enable the Datadog Cluster Agent](#step-2-enable-the-datadog-agent).

[1]: https://github.com/DataDog/datadog-agent/blob/master/Dockerfiles/manifests/cluster-agent/dca-secret.yaml
{{% /tab %}}
{{% tab "Environment Variable" %}}

1. Run the following command to create a secret token:

    ```
    echo -n '<ThirtyX2XcharactersXlongXtoken>' | base64
    ```

2. Refer to this secret with the environment variable `DD_CLUSTER_AGENT_AUTH_TOKEN` in the manifests of the Cluster Agent and the node-based Agent.

    ```
              - name: DD_CLUSTER_AGENT_AUTH_TOKEN
                value: "<ThirtyX2XcharactersXlongXtoken>"
    ```

{{% /tab %}}
{{% tab "ConfigMap" %}}

1. Run the following command to create a secret token:

    ```
    echo -n '<ThirtyX2XcharactersXlongXtoken>' | base64
    ```

2. Create your `datadog-cluster.yaml` with the variables of your choice within the `datadog.yaml` file and create the ConfigMap accordingly:

    ```
    kubectl create configmap dca-yaml --from-file datadog-cluster.yaml
    ```

{{% /tab %}}
{{< /tabs >}}

**Note**: This needs to be set in the manifest of the Cluster Agent **and** the node agent.

### Step 3 - Create the Cluster Agent and its service

1. Download the following manifests:

  * [`datadog-cluster-agent_service.yaml`: The Cluster Agent Service manifest.][2]
  * [`cluster-agent.yaml`: Cluster Agent manifest][3]

2. In the `cluster-agent.yaml` manifest, replace `<DD_API_KEY>` with [your Datadog API key][4]:

3. In the `cluster-agent.yaml` manifest, set the token from [Step 2 - Secure Cluster-Agent-to-Agent Communication](#step-2-secure-cluster-agent-to-agent-communication). The format depends on how you set up your secret; instructions can be found in the manifest directly.

4. Run: `kubectl apply -f datadog-cluster-agent_service.yaml`

5. Finally, deploy the Datadog Cluster Agent: `kubectl apply -f cluster-agent.yaml`

### Step 4 - Verification

At this point, you should see:

```
-> kubectl get deploy

NAME                    DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
datadog-cluster-agent   1         1         1            1           1d

-> kubectl get secret

NAME                   TYPE                                  DATA      AGE
datadog-auth-token     Opaque                                1         1d

-> kubectl get pods -l app=datadog-cluster-agent

datadog-cluster-agent-8568545574-x9tc9   1/1       Running   0          2h

-> kubectl get service -l app=datadog-cluster-agent

NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP        PORT(S)          AGE
datadog-cluster-agent   ClusterIP      10.100.202.234   none               5005/TCP         1d
```

## Configure the Datadog Agent

After having set up the Datadog Cluster Agent, configure your Datadog Agent to communicate with the Datadog Cluster Agent.

### Setup
#### Step 1 - Set Configure RBAC permissions for node-based Agents

1. Dowload the the [`rbac-agent.yaml`][5] manifest. Note that when using the Cluster Agent, your node Agents are not able to interact with the Kubernetes API server—only the Cluster Agent is able to do so.

2. Run: `kubectl apply -f rbac-agent.yaml`

#### Step 2 - Enable the Datadog Agent

1. Dowload the [`agent.yaml` manifest][6].

2. In the `agent.yaml` manifest, replace `<DD_API_KEY>` with [your Datadog API key][4]:

3. In the `agent.yaml` manifest, set the token from [Step 2 - Secure Cluster-Agent-to-Agent Communication](#step-2-secure-cluster-agent-to-agent-communication). The format depends on how you set up your secret; instructions can be found in the manifest directly.

4. Create the DaemonSet with this command: `kubectl apply -f agent.yaml`

### Verification

Run:

```
kubectl get pods | grep agent
```

You should see:

```
datadog-agent-4k9cd                      1/1       Running   0          2h
datadog-agent-4v884                      1/1       Running   0          2h
datadog-agent-9d5bl                      1/1       Running   0          2h
datadog-agent-dtlkg                      1/1       Running   0          2h
datadog-agent-jllww                      1/1       Running   0          2h
datadog-agent-rdgwz                      1/1       Running   0          2h
datadog-agent-x5wk5                      1/1       Running   0          2h
[...]
datadog-cluster-agent-8568545574-x9tc9   1/1       Running   0          2h
```

Kubernetes events are beginning to flow into your Datadog account, and relevant metrics collected by your Agents are tagged with their corresponding cluster level metadata.

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://github.com/DataDog/datadog-agent/tree/master/Dockerfiles/manifests/cluster-agent/rbac
[2]: https://github.com/DataDog/datadog-agent/blob/master/Dockerfiles/manifests/cluster-agent/datadog-cluster-agent_service.yaml
[3]: https://github.com/DataDog/datadog-agent/blob/master/Dockerfiles/manifests/cluster-agent/cluster-agent.yaml
[4]: https://app.datadoghq.com/account/settings#api
[5]: https://github.com/DataDog/datadog-agent/blob/master/Dockerfiles/manifests/cluster-agent/rbac/rbac-agent.yaml
[6]: https://github.com/DataDog/datadog-agent/blob/master/Dockerfiles/manifests/agent.yaml
