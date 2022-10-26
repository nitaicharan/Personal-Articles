# Kube-hunter

Created: May 7, 2022 10:47 AM
Finished: No
Source: https://kube-hunter.aquasec.com/
Tags: #tool

![Kube-hunter%2067ca5b2cf1e74ba5b4eda334d3c51aa1/KubeHunter_Watermark.png](Kube-hunter%2067ca5b2cf1e74ba5b4eda334d3c51aa1/KubeHunter_Watermark.png)

![Kube-hunter%2067ca5b2cf1e74ba5b4eda334d3c51aa1/KubeHunter_matrix.jpg](Kube-hunter%2067ca5b2cf1e74ba5b4eda334d3c51aa1/KubeHunter_matrix.jpg)

From outside the cluster, kube-hunter probes a domain or address range for open Kubernetes-related ports, and tests for any configuration issues that leave your cluster exposed to attackers. You’ll get a full report that highlights these security concerns. The source code is available on [GitHub](https://github.com/aquasecurity/kube-hunter) and we welcome contributions to extend the set of tests.

![Kube-hunter%2067ca5b2cf1e74ba5b4eda334d3c51aa1/report-example-2.png](Kube-hunter%2067ca5b2cf1e74ba5b4eda334d3c51aa1/report-example-2.png)

## Where does kube-hunter run?

Start by running kube-hunter as a container on any machine outside your cluster, and when prompted, give it the domain name or IP address of the cluster. This gives an attackers-eye-view of your Kubernetes setup.

You can run kube-hunter on a machine in the cluster, and select the option to probe all the local network interfaces.

You can also run kube-hunter as a pod within the cluster. The report will give you an indication of how exposed your cluster would be in the event that one of your application pods is compromised (through a software vulnerability, for example).

## What tests does kube-hunter run?

Kube-hunter tests are classified into “passive” and “active”, and by default kube-hunter only runs passive tests (or “hunters”).

A passive hunter will never change the state of the cluster, while an active hunter can potentially do state-changing operations on the cluster, which could be harmful. If you want to also run the active hunters you need to specify –active when running the command.

Here’s the set of currently implemented tests in kube-hunter. If you have ideas for additional tests we would love you to suggest them through issues or even pull requests in the [kube-hunter GitHub repo](https://github.com/aquasecurity/kube-hunter/)

## Passive Tests