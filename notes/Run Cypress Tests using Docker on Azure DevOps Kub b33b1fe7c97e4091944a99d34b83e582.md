# Run Cypress Tests using Docker on Azure DevOps Kubernetes Agents

Created: September 27, 2022 8:21 PM
Finished: No
Source: https://medium.com/adessoturkey/run-cypress-tests-using-docker-on-azure-devops-kubernetes-agents-6424c56e8503

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1j7ih2zzcCyifrZJ2lOy0FQ.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1j7ih2zzcCyifrZJ2lOy0FQ.png)

Cypress is an **end-to-end testing framework for web test automation**. It enables front-end developers and test automation engineers to write automated web tests. It is a life-saving tool and helps developers quite a lot. And it is really easy to use, even easier when you decide to run Cypress tests via Docker. This feature helps us automate Cypress during a Continuous Integration process. All you have to do is run a simple Docker command inside a pipeline and let it do all the work.

In this article, I will demonstrate how to implement a pipeline that uses a Kubernetes agent running Cypress tests using Docker. There will be 7 steps:

# **1- Create a PAT in Azure DevOps**

> Sign in to your organization (https://dev.azure.com/{yourorganization}). And go to Personal Access Tokens section.
> 

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1Ly5V2WYCUEmhvy9FqB9vSw.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1Ly5V2WYCUEmhvy9FqB9vSw.png)

> Click on “New Token”
> 

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1Q9c1wl1fSnpWEIV12cP3aQ.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1Q9c1wl1fSnpWEIV12cP3aQ.png)

> For our purpose, it is enough to give Agent Pools(Read&Manage) and Auditing(Read Audit Logs) permissions. Save the token.
> 

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1b72aHVuyGyerZ1RPDIlHoQ.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1b72aHVuyGyerZ1RPDIlHoQ.png)

# **2- Create an Agent Pool by clicking on “Add Pool”**

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1OzoOBhA-GWQ95B4L__TNuw.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1OzoOBhA-GWQ95B4L__TNuw.png)

# **3- Create the image for the agent**

> Build your image docker build -t {your-registry-name}/agent/azure-agent:v1.0 .
> 

*NOTE: Put* **start.sh** *and* **Dockerfile** *into the same location*

> Before building your image, check the architecture of your Kubernetes nodes to configure FROM --platform=... ubuntu:18.04 section.
> 

***Note:*** If you want a docker ubuntu image in which you can run docker and systemctl commands, you can check [petschenek/ubuntu-20.04-dind](https://hub.docker.com/repository/docker/petschenek/ubuntu-20.04-dind) and [petschenek/ubuntu-22.04-dind](https://hub.docker.com/repository/docker/petschenek/ubuntu-22.04-dind) images.

# **4- Create the deployment on the Kubernetes cluster**

Here, I want to explain some details about this deployment YAML. First, I created an ***emptyDir*** volume inside the pod. The filesystems of the containers that are going to be created inside the pod should be a normal filesystem, not a copy-on-write filesystem like AUFS and BTRFS. And since we cannot run AUFS on top of AUFS, I mounted “***/var/lib/docker***” on a normal filesystem with the help of ***emptyDir*** volume***.*** Then I added “***privileged: true***” security context for docker to run successfully. Then the rest is easy. I just passed relevant information into the ado-agent-1 container as environment variables. Just for simplicity, I put the values of the environment variables in plain text but you should not do that :). Use at least Kubernetes secrets to store those values.

# **5- Check whether the agent is listening for jobs**

> You can check the pod logs where you should see “Listening for jobs” at the end:
> 

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/18-Fac5HU4fnQmcgzo-faEA.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/18-Fac5HU4fnQmcgzo-faEA.png)

> and check the agent pool where agent-1 should be online:
> 

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1vVDthCqJt5liPoHYQ7_efQ.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1vVDthCqJt5liPoHYQ7_efQ.png)

# **6- Create the Azure Pipeline**

You can also add the “***baseUrl: http://{project-url}:{port}***” and “***video: true***” options to the **cypress.json** file directly and remove the section where these values are passed as environment variables to the docker container.

# **7- Run the pipeline and observe the results**

> Check the test results
> 

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1rdCQiy-Ns_1t3FRfzc_y9w.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1rdCQiy-Ns_1t3FRfzc_y9w.png)

> Download the screenshots and videos
> 

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1NEY8oKJjguk8FrjypVwjEQ.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1NEY8oKJjguk8FrjypVwjEQ.png)

![Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1JjZCDFuS21yAGcVLszaGTQ.png](Run%20Cypress%20Tests%20using%20Docker%20on%20Azure%20DevOps%20Kub%20b33b1fe7c97e4091944a99d34b83e582/1JjZCDFuS21yAGcVLszaGTQ.png)

# CONCLUSION:

As can be seen, we can run Cypress tests via Docker inside a Kubernetes pod. This gives us flexibility because we do not need to install a bunch of packages for Cypress to run and do not face dependency issues.

# RESOURCES:

[https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/)