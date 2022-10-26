# Build a Fully-Automated DevOps Pipeline with GitOps

Created: September 28, 2022 9:18 AM
Finished: No
Source: https://medium.com/@Anita-ihuman/build-a-fully-automated-devops-pipeline-with-gitops-5dbf490b06c9
Tags: #gitops

![Build%20a%20Fully-Automated%20DevOps%20Pipeline%20with%20GitOp%20ec6539e8d6284fcf9c577bc3d51a64de/1EPa-9VbOqL5OH3Xkg-StaA.png](Build%20a%20Fully-Automated%20DevOps%20Pipeline%20with%20GitOp%20ec6539e8d6284fcf9c577bc3d51a64de/1EPa-9VbOqL5OH3Xkg-StaA.png)

What is GitOps

With the advancement in modern-day technologies, most teams are accelerating the software development lifecycle through automation. However, to attain total agility in a software development pipeline, DevOps teams require technologies to deploy software applications while maintaining IT infrastructure effectively continually. In this article, I will introduce an operational framework called GitOps that provides solutions to these demands. I will discuss its components and how GitOps works. I will highlight its principles, benefits and challenges. I’ll also discuss what distinguishes GitOps from DevOps and list the top GitOps tools.

# Why GitOps?

Previously, managing IT infrastructure was a complex task. Most infrastructure configurations were manually managed, configured and distributed by system administrators(IAC). And, because different team members simultaneously collaborated on these infrastructures, this often resulted in multiple errors.

However, recent technologies are adapting new methods of designing, developing, documenting and maintaining IT Infrastructure to make it easier to edit and distribute configuration specifications among teams. DevOps and engineering teams can now create & store infrastructure files locally, execute and execute locally using a version control system.

Even though the introduction of IAC brought a lot of benefits to DevOps teams, this process also had its challenges. Since it allowed multiple teams to collaborate in the infrastructure configuration simultaneously, it required a review and approval process. It lacked tests to confirm changes from individual team members, and infrastructure updates were done manually. DevOps teams needed an automated, scalable way to document and provision application infrastructure; **[GitOps**.](https://about.gitlab.com/topics/gitops/)

# What is GitOps?

GitOps is an operational paradigm that extends DevOps best practices for application development and integrates them into infrastructure automation(IAC). GitOps is a blend of DevOps best practices such as version control, code review, and CI/CD pipelines to store and manage all data, documentation, and operational tasks for Kubernetes. It Allows you to manage architecture and rapidly reproduce the system’s cloud infrastructure by utilising git as the single source of truth for comprehensive automated testing and deployment. GitOps expands the capability of IaC by keeping the code, testing, staging, and production environments in sync. Similar to how application source code creates the same application binaries each time it is generated, GitOps configuration files provide the same infrastructure environment each time it is deployed.

# Three components of GitOps

GitOps is [no single tool or product](https://www.weave.works/technologies/gitops/#benefits-of-gitops); Instead, it combines IAC and complete DevOps pipeline practices. To get started with GitOps, you should consider these components.

- Infrastructure as code (IaC)

GitOps used a version control system called **git** as the single source of truth for handling and storing application infrastructure as code. IAC defines infrastructure using configuration files(code) rather than manually using a graphical user interface. Infrastructure can be easily procured through configuration files that hold the infrastructure specifications, making it easy to edit and distribute configuration. DevOps teams use this to improve the infrastructure’s consistency, stability and scalability. Collaboratoration to the infrastructure configuration and provisioning takes the same flow as the application source code.

- Merge Requests (MRs)

Merge requests (MRs) are the modification method used by GitOps for any updates to the infrastructure. Teams can collaborate on the infrastructure through reviews and comments before changes are officially approved in the main branch. A merge request acts as an audit log and helps keep track of the changes implemented by each team member. It is used to deploy and validate system infrastructure changes automatically. With an inbuilt roll-back feature, revert changes that are to the desired version.

- Continuous integration and continuous deployment (CI/CD)

In a GitOps environment, infrastructure management is automated using a CI/CD(Continuous integration and Continuous deployment) pipeline. So, whenever a code is merged to the main branch, CI/CD acts as a reconciliation loop. It initiates the change from the git repository to the environment. GitOps automation and continuous deployment eliminate human errors that arise when infrastructure configuration is done manually.

# How does GitOps work?

GitOps allows developers to manage infrastructure automation of various systems such as VMs and containers; it is especially popular among teams managing Kubernetes-based infrastructure. GitOps guarantees that a system’s cloud lives infrastructure is reconfigured and syncs with the state of a Git repository as soon as the merged request is approved.

Consider it [this way](https://thenewstack.io/what-is-gitops-and-why-it-might-be-the-next-big-thing-for-devops/), assume you developed a central repository containing all of the configuration files (YAML files), documentation, and code required for Kubernetes. You then automated it such that other system administrators responsible for Kubernetes deployment can simply clone the repository, modify the code, and submit a merge request to the central Git repository.

[Build%20a%20Fully-Automated%20DevOps%20Pipeline%20with%20GitOp%20ec6539e8d6284fcf9c577bc3d51a64de/0PaLTjr-s2NmQvwyD](Build%20a%20Fully-Automated%20DevOps%20Pipeline%20with%20GitOp%20ec6539e8d6284fcf9c577bc3d51a64de/0PaLTjr-s2NmQvwyD)

GitOps Workflow environment

The workflow of a typical GitOps environment looks like this;

- You make a pull request for a new feature to IAC, configuration, or application code on the Git repository.
- The code will get reviewed and the changes approved.
- After that, the code gets merged into the Git repository.
- Immediately CI build pipelines are established and triggered from pull requests. It runs a few checks, and if all of them pass, it creates a new image and sends it to the image container.
- The deployment Automator identifies changes to the image repository when the merge request is finished, grabs it from the registry, and updates the YAML configuration file.
- This declares the changes operational, and the modification is automatically distributed to the cluster.

# Benefits of GitOps

- **Allows team collaboration**

Since it uses version control, respective members can simultaneously contribute to the configurations. Team members can create a merge request that suggests changes to the code. There would not be any cases of conflicting changes as every new configuration goes through the review process before it’s approved.

- **Version control**

GitOps is a version control system; therefore, it has all of the capabilities of git, such as detailed audit records of all changes made. Details like the committer’s identity, the commit timestamp, and the commit ID are saved when a commit is made. As a result, you have total access to the system and can track all changes made to the infrastructure configuration.

- **Increases reliability**

Manually configuring your application infrastructure can be unreliable, but with an automated system, you can achieve fewer errors and faster problem resolution. GitOps rescues you against configuration drift and snowflake environment by combining DevOps techniques and IAC. Also, since git is the single truth source, you can be sure of a reliable system.

- **Automation**

Automating the entire deployment process saves time and increases productivity for a DevOps team. You can even increase the number of changes made to the configuration and spend more experimenting with new infrastructure configurations. The CI/CD methods are utilised to keep your infrastructure operational and lower downtime even when these modifications are being deployed.

# GitOps vs DevOps: what’s the difference?

Although GitOps is a branch of DevOps, determining which approach is the best option depends on the project’s objectives and goals. DevOps is a philosophy based on collaborative efforts between developers and operators to accelerate the software development life cycle. While GitOps focuses on delivery, DevOps is significantly broader in scope since it addresses a broader set of issues such as CI, CD, visibility, and governance. In GitOps, the git repository is the source of truth for the deployment state, whereas, in DevOps, it is the application or server configuration files. Furthermore, [DevOps may be applied to every process](https://spectralops.io/blog/gitops-vs-devops/) within an application, while GitOps is commonly used in combination with containerisation technologies such as Kubernetes.

# GitOps Tools & Technologies

Some tools and technologies that can be sued to achieve GitOps procedures include:

- [MeshMap](https://layer5.io/cloud-native-management/meshmap)
- [Flagger](https://flagger.app/)
- [Ansible](https://www.ansible.com/)

# Conclusions

GitOps is a methodology that combines DevOps practices with IAC to provide a comprehensive application development lifecycle and is quickly becoming a widespread practice in modern cloud infrastructure. GitOps employs git as its single source of truth, making it more trustworthy and straightforward for developers to get started. Adopting GitOps methods allows you to leverage the most recent DevOps principles and technologies to create a high-quality software development environment. Teams can be more productive and consistent in their application development while managing the current cloud infrastructure. Finally, while some businesses may take time to embrace GitOps and see the benefits, this methodology is still expanding in capabilities and popularity.