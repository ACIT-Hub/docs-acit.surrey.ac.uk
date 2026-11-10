## Common RIE tasks related to accelerated compute 

As a junior Research Infrastructure Engineer (RIE), you help keep HPC and accelerated compute systems running smoothly and usable for researchers. This section introduces typical tasks you might encounter and starts to build your understanding of the terminology and workflows involved. 

### 1. HPC / Slurm User Account Creation 

One of the first tasks many junior RIEs take on is creating and managing user accounts for HPC access. This ensures that only authorised users can run jobs and that their usage can be tracked. 

Slurm is used to queue and schedule jobs on shared HPC clusters, deciding which jobs run, on which nodes, and when. You’ll learn more about Slurm later and in the future module on scheduling. 

One of the first tasks many junior RIEs take on is creating and managing user accounts for HPC access. This ensures that only authorised users can run jobs and that their usage can be tracked. 

Slurm is used to queue and schedule jobs on shared HPC clusters, deciding which jobs run, on which nodes, and when. You’ll learn more about Slurm later and in the future module on scheduling. 

A typical workflow might look like this: 

**1. Receive a request** 

  - A new user (e.g. PhD student, postdoc) or PI submits a request via a form or ticketing system. 
  - The request usually includes name, institutional username, department, and project or group. 

**2. Verify identity and eligibility** 

  - Check the user exists in the institutional identity system (e.g. Microsoft Active Directory, LDAP). 
  - Confirm they are eligible for HPC access (e.g. staff/student, part of a project with an allocation). 
  - If the user is from another institution, check whether their institution is part of an identity federation that your home institution accepts. If not, follow your local process for creating a local or sponsored account, and record its expiry date and any conditions of use. 
  
**3. Create or link the Linux account** 

  - If your HPC uses local accounts, create a Linux user with the correct username and UID/GID. 
  - If using LDAP/AD integration, ensure the account is visible on HPC nodes and belongs to appropriate groups. 
  - If the user is from another institution, check whether their institution is part of an identity federation that your home institution accepts. If not, follow your local process for creating a local or sponsored account, and record its expiry date and any conditions of use. 
  
**4. Create the Slurm account / association** 

  - Use tools such as Ansible playbooks or Slurm commands to:  
  - Create a Slurm account or association (e.g. tied to a project or PI). 
  - Add the user to the correct Slurm account and QoS/partition access. 
  - Check that the user appears in Slurm accounting (e.g. via sacctmgr show user). 
  
**5. Add access to related services** 

  - If needed, add the user to project storage groups, GitLab groups, or other services. 
  - Update any internal records or documentation.
   
**6. Test and confirm** 

  - Optionally test a simple login or job submission (e.g. with the user or a test account). 
  - Inform the user that access is ready and provide links to documentation. 



### 2. Software Installation on an HPC Cluster  

Researchers rely on a wide range of software: compilers, libraries, domain‑specific tools, and AI/ML frameworks. Junior RIEs often help with installing and maintaining software stacks on HPC nodes. 

A typical installation process might include: 

**1. Gather requirements** 

  - Understand what the user needs: software name, version, dependencies, and how they plan to use it (CPU vs GPU, MPI, Python, etc.). 
  - Check whether the software is already available in another version or module. 

**2. Test and confirm** 

  - For simpler packages, use Linux package managers (e.g. yum, dnf, apt-get). 
  - For complex HPC software, use tools like EasyBuild to build and install in a controlled, reproducible way. 

**3. Prepare the environment** 

  - Ensure build tools and compilers are available (e.g. GCC, Intel, NVIDIA HPC SDK). 
  - Check required libraries (e.g. MPI, BLAS, CUDA) are installed or plan to install them. 

**4. Install and configure** 

  - Run the installation commands or EasyBuild recipes. 
  - Resolve dependency issues (e.g. missing libraries, incompatible versions). 
  - Create or update environment modules (Lmod) so users can load the software with module load. 
  
**5. Test the installation** 

  - Run a simple test (e.g. --version, a small example program, or a sample script). 
  - Confirm that the software works on the relevant nodes (CPU, GPU, login vs compute). 
  
**6. Document and communicate**  

  - Add the software to internal documentation or an ‘available modules’ list. 
  - Inform the requesting user and, if relevant, the wider community. 

**6. Licence management (where applicable)** 

  - For licensed software, check licence availability and configuration (e.g. licence server). 
  - Keep track of licence usage and renewal dates. 

!!! question "Challenge"

    A researcher asks for “Python 3.11 with PyTorch and CUDA support” on the GPU partition. Write down what information you would ask for and outline the steps you would take to install and expose this via modules.

    ??? success "Show our answer"

        - Gather requirements from the researcher
        - Check current available software documentation vs the requirements from the researcher
        - Check cluster compatibility  i.e NVIDIA driver for NVIDIA GPU's, existing python and CUDA modules, ensure pytorch supports python 3.11 
        - install the software
        - create environment module, set the virtual environment, add dependencies and executable paths
        - Test the installation
        - Deploy and update the cluster documentation

### 3. Project Spaces: Storage Creation and Access

Many research groups need dedicated storage for their projects. As a junior RIE, you may help with creating and managing project spaces on high‑performance storage. 

A typical workflow: 

**1. Receive a storage request** 

  - A PI or project lead submits a request form or ticket, specifying project name, required capacity, and expected duration. 

**2. Check policies and quotas** 

  - Review storage policies (e.g. free baseline quota vs paid extensions). 
  - Confirm the request fits within available capacity and policy rules. 

**3. Create the project directory** 

  - On the storage system (e.g. Dell Isilon, Quantum, Weka), create a new directory with a clear naming convention (e.g. /projects/AI-Climate-2026). 
  - Set appropriate permissions (Unix groups, ACLs) so only authorised users can access it. 


**4. Set quotas and performance settings** 

  - Apply a quota (e.g. 10 TB) and any performance or tiering settings required. 
  - Record quota and project details in internal tracking systems. 
  
**5. Add users and groups** 

  - Ensure project members are in the correct Unix group or ACL. 
  - Coordinate with storage engineers if more complex access patterns are needed. 

**6. Test access** 

  - Confirm that project members can mount or access the space from HPC nodes or other systems. 
  - Check that usage is reported correctly in monitoring tools. 

**7. Communicate and document** 

  - Inform the project team that the space is ready, including path, quota and any usage guidelines. 
  - Update documentation or inventory to track the project space. 

### 4. Troubleshooting HPC and Accelerated Compute Issues

A large part of the RIE role involves troubleshooting hardware and software issues on HPC clusters and GPU nodes. Problems may be detected via monitoring or reported by users. 

A typical troubleshooting workflow: 

**1. Review the ticket or report** 

  - Read the description carefully: what is the user trying to do, what error do they see, when did it start? 
  - Check for any screenshots, error messages or job IDs. 


**2. Gather basic information** 

  - Identify the user, job ID, partition, nodes involved, and any relevant time window. 
  - Check whether other users are reporting similar issues. 

**3. Check job and cluster status** 

  - Use Slurm commands (e.g. squeue, scontrol show job, sinfo) to see job state and node health. 
  - Look at job reasons (e.g. Resources, QOSMaxGRESPerUser, NodesDown). 

**4. Inspect the environment and logs** 

  - Check environment modules or containers used by the job. 
  - Review logs (e.g. system logs, Slurm logs, application logs) for errors or warnings. 
    - If a multi‑node job fails on one node, you may see errors in Slurm and MPI logs that relate to node‑to‑node communication. 

**5. Identify likely cause** 

  - Decide whether the issue is likely:  
    - User script / configuration (e.g. wrong partition, missing modules). 
    - Cluster configuration (e.g. misconfigured partition, QoS limits). 
    - Hardware (e.g. memory errors, GPU faults). 
  - For hardware issues, mark nodes as drained or down in Slurm and escalate to hardware support. 

**6. Test and confirm** 

 - Run a small test job or command to reproduce the issue (if appropriate). 
 - Confirm that your fix resolves the problem. 

**7. Respond to the user and document** 

 - Explain what went wrong and how it was fixed, in clear language. 
 - Update any runbooks or documentation if this is a recurring issue. 

### 5. Lifecycle Management of HPC Hardware and Software

Junior RIEs often assist more senior colleagues with parts of the HPC cluster lifecycle, from procurement to deployment. 

#### Hardware‑related steps might include

**1. Requirements gathering** 

 - Help collect information from users or stakeholders about expected workloads (CPU vs GPU, memory, storage, network needs).  
  
**2. Procurement support** 

 - Assist with preparing technical specifications for quotes. 
 - Help compare vendor proposals (e.g. GPU types, warranty, support). 

**3. Delivery and racking** 

  - Support the team in physically racking servers and storage in the data centre (under supervision). 
  - Help with network cabling and labelling.  

**4. Hardware management configuration** 

  - Assist in configuring iLO, iDRAC or BMC interfaces for remote management. 
  - Verify that hardware monitoring tools can see the new nodes. 

#### Software‑related steps might include

**1. OS deployment** 

 - Use tools like Foreman or PXE boot to install a base Linux OS on new nodes. 
 - Check that nodes boot correctly and appear in management systems. 
  
**2. Configuration management** 

 - Run or maintain Ansible or Chef playbooks to apply standard configurations (e.g. packages, security settings, mount points). 
 - GPU‑enabled software (e.g. PyTorch with CUDA) needs to be installed on nodes with GPUs and configured so jobs request those nodes correctly. 
 - Help troubleshoot any nodes that fail to configure correctly. 

**3. Scheduler and services setup** 

  - Assist in installing and configuring Slurm daemons on new nodes. 

### 6. Monitoring and Observability

Monitoring is essential for keeping accelerated compute and HPC systems healthy. Junior RIEs often help with proactive monitoring and responding to alerts. 

Typical monitoring tasks: 

**1. Check dashboards regularly** 

 - Use tools like Grafana (fed by Prometheus or VictoriaMetrics) to view metrics such as CPU/GPU utilisation, memory usage, node health, storage capacity, and hardware temperature.  

**2. Review alerts** 

 - Respond to alerts from monitoring systems (e.g. node down, high error rates, cooling faults, storage nearing capacity). 
 - Prioritise alerts based on severity and impact. 
  
**3. Investigate anomalies** 

 - If a node shows unusual behaviour (e.g. always idle or always at 100% utilisation), investigate:  
  - Check jobs running on that node. 
  - Look at logs for hardware or software errors. 
  
**4. Escalate or act** 

 - For minor issues, take immediate action (e.g. restart a service, drain a node). 
 - For major or unclear issues, escalate to senior RIEs or relevant teams. 
  
**5. Improve monitoring** 

 - Suggest new metrics or dashboards based on recurring issues. 
 - Help refine alert thresholds to reduce noise while catching real problems. 
  

### 7. Tools Commonly Used by RIE Teams

RIEs use a wide range of tools to manage infrastructure and collaborate. As a junior RIE, you will gradually become familiar with tools such as: 

| Category                                  | Tools                                                                                                     |
| ----------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| Ticketing and workload management         | GitLab Issue Boards, Jira                                                                                 |
| Network and inventory management          | Infoblox (DNS/DHCP/IPAM), NetBox (data centre inventory and asset management), OPNsense (firewall rules) |
| Virtual private network (VPN)             | Tailscale, WireGuard                                                                                      |
| Hardware monitoring                       | OpenManage, iDRAC/iLO/BMC interfaces, Grafana                                                             |
| OS deployment and configuration           | Foreman, Ansible AWX, Chef                                                                                |
| Communication and documentation           | MS Teams/Zoom, Outlook/365, Atlassian Confluence, Slack                                                   |
| Code and CI/CD                            | GitLab for code and configuration management                                                              |
| Virtualisation and cloud                  | Azure, VMware, Proxmox, OpenNebula for VMs and private cloud services                                     |
| Security information and event management | Wazuh (SIEM, vulnerability monitoring and alerting)                                                       |
| Identity access management                | Keycloak, FreeIPA (manage user identities centrally)                                                      |


 
You are not expected to know all of these on day one, but you will gradually learn which tools your team uses and how they fit into the overall infrastructure. 

**Try it yourself:**
From the list above, pick a tool you haven’t heard of. Research this tool to discover more about what it can be used for. Do you know if your team is already using it? If not, do you think it would be useful for your team? 

As you progress, you will move from following existing runbooks and instructions to helping design and improve them. The aim of this introductory module is not to make you an expert in all these tasks, but to give you a clear picture of what junior RIEs do and a detailed starting point for learning the terminology and workflows you will encounter. 

### 8. Try it yourself: RIE scenarios 

**Scenario 1: GPU job pending** 

![h5p](slurm-job-issue.h5p)

**Scenario 2: New project storage** 

**Scenario 3: Account creation**
![h5p](scenario-account-creation-3.h5p)