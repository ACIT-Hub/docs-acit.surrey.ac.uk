# Ethics and sustainable compute 


Ethics and sustainability are central to how research infrastructure is designed, operated, and used. As an RIE, the decisions you make about storage, scheduling, monitoring, and access can directly affect people’s privacy, the fairness of resource allocation, and the environmental impact of your systems.  

This section introduces the key ethical and sustainability questions you should keep in mind when working with compute, storage, and data services. We won’t be going into lots of detail, but we would like to give you enough awareness to spot potential issues and to keep these concepts in mind when thinking about all aspects of your RIE role. 

## What is ethics and why is it relevant to RIEs?

Ethics are standards of right and wrong that prescribe what humans should do, usually in terms of rights, obligations, benefits to society, fairness, or specific virtues. In research, universities strive to do a positive net good whilst mitigating real or potential harm.  

Ethical practices can ensure: 

- people are protected, including research participants and staff 
- avoids harm, such as data misuse, breaches or unfair access to resources 

As an RIE, you have access to a lot of data; including data about people. This could be anything from genetic data to recordings of peoples’ voices. You will also have access to data about people using the infrastructure you maintain. These kinds of information are often referred to as personal or sensitive data. We must all follow UK GDPR and the Data Protection Act 2018, plus any institutional policies specific to where you work. Your institute will provide you with important training covering these laws and policies.  

But these laws and policies all mean that as an RIE it is important to know who can access what data and how long data is kept for. You will also need to consider: 

- Using approved storage and services 
- Applying least privilege – give users only the access they genuinely need 
- Ensuring encryption and secure transfer methods are used where required 
- Ensuring platforms are secured and regularly maintained to prevent breaches and leaks 
- Ensuring users are informed of their ethical obligations in using the system and follow security best practice. HPC is a shared resource and are often not secured for processing confidential data, RIEs are obligated to ensure that users are aware of this by providing a clear usage policy. Be clear with your data retention policies and check if researchers have a DMP (Data Management Plan), DPIA (Data Protection Impact Assessment) and/or ethical approval in place and review them in the context of the platforms you are administering, ensure it is followed throughout the various phases of the project.
- UK and US export control law restrictions determine who can be given access to certain systems, software, GPU hardware and the technical knowledge. Granting remote access to a piece of software counts as an export. When in doubt pause, escalate internally looping in the Security Lead and get the decision in writing. If in doubt, don't go ahead!

If you spot someone handling data in a way you may not think is ethical, make sure you know who to report this kind of issue to. In most cases, it is better to have the conversation with the person involved or with your line manager to avoid escalation of possible misunderstandings. Should the concern be more serious this may require a Data Protection officer for any GDPR concerns. Where the issue cannot be resolved internally there are external routes to the ICO Information Commissioners Office (ICO) or UK Research and Innovation (UKRI). It’s better to discuss it than to ignore a potential issue.  

Fairness is also a part of ethics. In your role, you will be involved in user access of compute resources. It is important to consider fair allocation of resource. To support fairness, there should be clear and transparent policies for access which are documented and accessible to users. You may also be involved in monitoring usage to spot issues around resource allocation. Another e-learning module will be developed to focus on monitoring.  

!!! question "Reflection point"
    Think of a situation where resource access could be unfair (e.g. one group submitting thousands of jobs). How might policies and monitoring help address this?

## Environmental impact of compute and sustainability

High-performance computing, GPUs, and large storage systems all consume a lot of energy and resources. As an RIE, you are in a key position to influence how sustainable these systems are. Sustainability is all about running services efficiently, reducing waste, and supporting institutional climate commitments while still enabling excellent research. 

In research computing, the energy used to power HPC and accelerated compute, plus related activities such as cooling, contributes to carbon emissions associated with electricity generation. There can also be hardware waste, when systems are replaced frequently or underused 

RIEs need to balance performance and capacity requirements with environmental impact and cost. This isn’t just about big decisions like procurement of new compute, but also small technical decisions, such as scheduling or data retention policies, can have large cumulative effects.   

**Example**: You notice that a GPU partition is almost always idle overnight, but nodes remain powered on. By working with your team, you introduce a policy to power down unused nodes after a certain time, and to consolidate jobs onto fewer nodes when demand is low. Over a year, this reduces energy use and costs without affecting research deadlines. 

Here’s some question to ask yourself when working as an RIE: 

- Are we using what we have efficiently, or powering idle capacity? 
- Do our scheduling and usage policies encourage efficient behaviour? 
- Are we monitoring the right metrics (e.g. utilisation, energy) to understand impact? 
- Can we automate waste‑reducing behaviours (e.g. powering down unused nodes, cleaning up temporary data)? 
- How can we communicate sustainability to users without blocking legitimate research? 
- How can we support users to request realistic CPU/GPU/memory/time limits? 

Keeping these questions in mind will help keep research infrastructure reliable and environmentally and financially sustainable. 

Lots of the questions in the list above will be discussed in more detail in future modules. Monitoring can include sustainability-specific monitoring tools such as Compute Energy & Emissions Monitoring Stack (CEEMS). You can 
[check out a demo of CEEMS](https://ceems.dev){target="_blank"}
, but we will discuss it further in the monitoring module.  

## Ethics scenarios

**Sensitive data scenario:**

!!! Sensitive data scenario "Challenge"
    A researcher wants to run jobs on the shared GPU cluster using a dataset that includes identifiable patient information. They mention that the data are stored on a general scratch filesystem

    Questions: 

    - What potential ethical and data protection issues do you see? 
    - Who would you talk to (e.g. data protection officer, ethics office, line manager)? 
    - What might you advise the researcher to do next? 

??? success "Suggested answers"
    1. What potential ethical and data protection issues do you see? 
        - The dataset contains identifiable patient information, which is personal and potentially special category data under UK GDPR and the Data Protection Act 2018. 
        - A shared GPU cluster and general scratch filesystem are often not designed or approved for storing or processing highly sensitive data; access may be too broad and controls insufficient. 
        - There is a risk of unauthorised access, data leakage or misuse, especially if permissions, encryption and retention policies are not appropriate. 


    2. Who would you talk to? 

        - Data Protection Officer or equivalent information governance contact. 
        - Research ethics office or the team responsible for ethical approvals. 
        - Your line manager or senior RIE, to ensure the issue is handled appropriately and consistently. 

    3. What might you advise the researcher to do next? 

        - Explain that general scratch storage and shared GPU clusters may not be appropriate for identifiable patient data without specific controls and approvals. 
        - Ask whether they have a Data Management Plan (DMP), Data Protection Impact Assessment (DPIA) and ethical approval, and suggest reviewing these in light of the current infrastructure. 
        - Recommend moving the data to an approved secure storage and compute environment designed for sensitive data (if available), or working with governance teams to identify one. 
        - Encourage them to consult the data protection/ethics contacts you’ve identified, and offer to help explain the technical aspects of the infrastructure to those teams. 
        - Emphasise that this is about protecting participants and the researcher, not blocking research, and that resolving it early is better than risking a breach. 

**Fairness scenario:** 


!!! Fairness scenario "Challenge"
    One group regularly submits thousands of long GPU jobs, and other users complain that they can’t get access to GPUs. 

    Questions: 

    - How might you use scheduling policies and monitoring to address this? 
    - Why is this an ethical issue as well as a technical one? 

??? success "Suggested answers"
    1. How might you use scheduling policies and monitoring to address this? 
        - Use monitoring (e.g. Slurm accounting, Grafana dashboards) to quantify: 
            - GPU usage by user/group over time. 
            - Queue wait times and job completion patterns. 
        - Review current scheduling policies (QoS, fair‑share, limits) to see whether they allow one group to monopolise GPUs. 
        - Introduce or adjust policies such as: 
            - Per‑user or per‑group GPU limits (e.g. maximum number of concurrent GPU jobs). 
            - Fair‑share weighting so resources are distributed more evenly across groups. 
            - Separate partitions/QoS for different types of workloads (e.g. short vs long jobs, interactive vs batch). 
        - Consider adding a “short” or “test” GPU QoS/partition so other users can run smaller jobs even when large jobs are queued. 
        - Communicate changes clearly, using monitoring data to show the current imbalance and the rationale for adjustments. 


    2.  Why is this an ethical issue as well as a technical one?

        - GPUs and HPC resources are often shared, limited institutional assets; allowing one group to dominate them can be unfair to other researchers. 
        - Unequal access can distort research opportunities, favouring some projects or groups over others without transparent justification. 
        - Transparent policies and monitoring help ensure that access is equitable and aligned with institutional priorities, and that decisions can be explained and justified. 

## Summary and further learning

**Summary**

In this module, you have: 

- Discovered how HPC and accelerated compute use multi‑core, multi‑node systems and GPUs to tackle large, complex problems. 
- Learned what a Research Infrastructure Engineer does and how the role differs from general sysadmin work. 
- Explored common junior RIE tasks related to accounts, software, storage, troubleshooting, lifecycle management and monitoring. 
- Considered ethics and sustainability and why they matter for RIEs. 

**Apply this in your environment**

- Identify one HPC/GPU system you support (or will support). 
- Find your local documentation on: 
    - Account creation and Slurm associations. 
    - Software installation and modules. 
    - Storage/project spaces. 
    - Monitoring dashboards. 
- Note down who to talk to about: 
    - Data protection and ethics. 
    - Sustainability and energy use. 
These actions will help you connect the concepts from this module to the specific tools and processes in your institution. 

**Further Learning**

Suggested next steps:

- Continue to the next ACIT Hub e-learning module: Introduction to scheduling.  
- Identify and review internal documentation for your HPC/accelerated compute environment. 
- Identify and complete institutional training on data protection, ethics and security: 
  - Data protection 
    - GDPR 
    - Research Data lifecycle (ingestion, processing, storage & deletion), storage locations i.e. scratch, persistent 
    - Data Protection Impact Assessment (DPIA) 
    - Institutional data classification 
  - Ethics
    - Institutional research ethics frameworks for approval processes 
    - Responsible use 
    - AI & ML ethics on HPC 
    - Reporting channels (to raise concerns over unethical use)  
    - Export controls 
  - Security 
    - Threat landscape: credential theft, supply chain attacks, ransomware 
    - Identity access: sso, ssh keypair hygiene 
    - Secure system administration 
    - Network security: network segmentation (management zones, compute), vlan design, vpns, firewall policies, bastion, host access 
    - Vulnerability management (CVE tracking and patching cadence) 
    - Log management and security monitoring 
    - Incidence response 
    - Acceptable use policy 
    - Software supply chain security 
    - Physical security 

You may also like to join one of the upcoming ACIT Hub events or watch a webinar recording. 