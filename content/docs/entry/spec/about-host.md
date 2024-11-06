---
Title: What is a "host"?
Date: 2020-09-02T12:44:26+09:00
URL: https://mackerel.io/docs/entry/spec/about-host
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613625783810
---

In Mackerel, a host is "the smallest unit that makes up the system". It can be a physical server, a virtual server, or even a managed service.

[f:id:mackerelio:20200902124054p:plain]

In order to begin monitoring with Mackerel, the monitoring target must first be registered as a host. Registering a host in Mackerel can be done several ways depending on the type of monitoring target.

<h2 id="howto-register-host-by-kinds">Host registration methods for different types of monitoring targets</h2>
Below are three typical methods for registering monitoring targets as hosts in Mackerel.

- [Register a host in Mackerel to monitor physical and virtual servers](#register-by-mackerel-agent)
- [Register a host in Mackerel to monitor container environments such as Kubernetes](#register-by-container-agent)
- [Register a host in Mackerel to monitor public cloud-provided managed services](#register-by-cloud-integration)

<h3 id="register-by-mackerel-agent">Register a host in Mackerel to monitor physical and virtual servers</h3>
To monitor a physical or virtual server with Mackerel, mackerel-agent must be installed onto your OS. mackerel-agent is the official monitoring agent software of Mackerel.
See the help page below.

[https://mackerel.io/docs/entry/howto/install-agent:title]

If the installed mackerel-agent works properly, host information will be registered in Mackerel as a "Standard host". For more information, see [Host types](#host-kinds).

<h3 id="register-by-container-agent">Register a host in Mackerel to monitor container environments such as Kubernetes</h3>
To monitor a container environment such as Kubernetes with Mackerel, mackerel-container-agent must be run as a sidecar container in your target environment.  mackerel-container-agent is Mackerel's official monitoring agent software for container environments.
See the help page below.

[https://mackerel.io/docs/entry/howto/container-agent:title]

If mackerel-container-agent is successfully run as a sidecar, host information will be registered in Mackerel as a "Micro host". For more information, see [Host types](#host-kinds).

<h3 id="register-by-cloud-integration">Register a host in Mackerel to monitor public cloud provided managed services</h3>
The following services can also be registered as hosts in Mackerel for integrated monitoring.

- Managed services provided by Amazon Web Services (AWS) such as Amazon RDS, etc.
- Managed services provided by Microsoft Azure such as Azure App Service, etc.

Mackerel uses cloud integration to monitor these managed services.

- [https://mackerel.io/docs/entry/integrations/aws:title]
- [https://mackerel.io/docs/entry/integrations/azure:title]

If successfully integrated, host information will be registered in Mackerel as either a "Standard host" or a "Micro host". For more information, see [Host types](#host-kinds).

<h2 id="host-kinds">Host types</h2>
A host registered in Mackerel is automatically classified as either a "Standard host" or a "Micro host". You cannot choose the host type when registering.

<h3 id="billing-unit">Host features</h3>
<h4 id="standard-host">Standard hosts</h4>
- The cost of a "Standard host" is applied as listed in [pricing](https://mackerel.io/pricing/).
- The cost of 1 standard host includes 200 metrics associated with that host.
	- See also [How usage fees are calculated](https://support.mackerel.io/hc/en-us/articles/31304727432729).

<h4 id="micro-host">Micro hosts</h4>
- The cost of a "Micro host" is applied as listed in [pricing](https://mackerel.io/pricing/).
- The cost of 1 micro host includes 30 metrics associated with that host.
	- See also [How usage fees are calculated](https://support.mackerel.io/hc/en-us/articles/31304727432729).


<h3 id="casestudy">Case studies</h3>
<h4 id="casestudy-mackerel-agent">Registering a host using mackerel-agent</h4>
The host will be registered as a standard host.

<h4 id="casestudy-mackerel-container-agent">Registering a host using mackerel-container-agent</h4>
The host will be registered as a micro host.

<h4 id="casestudy-integration-standard-host">Registering a VM-type service using cloud integration</h4>
The host will be registered as a standard host. VM-type services refer to the following.

- Amazon EC2
- Azure Virtual Machines
- Google Compute Engine

These can also be used together with mackerel-agent installation. See below for more details.

[https://support.mackerel.io/hc/articles/360040109451:title]

<h4 id="casestudy-integration-micro-host">Registering something other than a VM service using cloud integration</h4>
The host will be registered as a micro host.

<h4 id="casestudy-register-by-api">Registering a host using the host registration API</h4>
Mackerel lets you register hosts using the [Host Registration API](https://mackerel.io/api-docs/entry/hosts#create).

If using this API, the host will be registered as a standard host. It is not possible to register as a micro host using the API.
