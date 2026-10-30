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