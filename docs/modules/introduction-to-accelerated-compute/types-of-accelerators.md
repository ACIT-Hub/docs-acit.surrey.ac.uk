# Types of accelerators

## Non-GPU Accelerators 

GPUs are not the only types of accelerators available. This section describes other types of accelerators. 

### ASIC 

Application-Specific Integrated Circuits (ASICs) are chips that are designed and fabricated to perform a particular function, such as network switching. Because they are purpose-built for a specific application, ASICs can be highly optimised, delivery improved performance, lower power consumption, and reduced cost at scale compared with general-purpose processors. Traditionally, ASICs have been used in applications such as network switching and mobile devices, but they are increasingly being developed for AI workloads (such as inferencing). Examples include Google’s Tensor Processing Unit (TPU) which was introduced in 2015. 

The primary drawbacks of ASICs is their lack of flexibility and the significant upfront investment required to develop them. Unlike programmable devices, ASICs are typically not reconfigurable after fabrication and can only perform the functions for which they were originally designed. In addition, the design, verification, and manufacturing of a custom ASIC involve substantial development costs and long lead times. As a result, ASICs are generally only economically viable when produced and deployed at sufficient scale to offset these initial costs 
Neural Processing Units (NPUs) are specialised accelerators designed to improve the performance and efficiency of AI workloads. They can be implemented as standalone processors or integrated into CPUs and GPUs. In recent years, NPUs have become increasingly common in consumer processors, enabling local AI capabilities on devices such as those powered by AMD Ryzen AI, Intel Core Ultra, and Qualcomm Snapdragon X platforms. 

NPU performance is typically expressed in TOPS (Tera Operations Per Second), representing trillions of operations per second. However, this metric should be treated with caution, as real-world AI performance is influenced by a range of factors beyond raw compute capability, including model architecture and size, memory bandwidth, software optimisation, and data movement efficiency. 

### FGPAs 

Field-Programmable Gate Arrays (FPGAs) are a type of integrated circuit that can be reprogrammed multiple times after manufacturing. This capability allows an FPGA to function as custom hardware (such as ASICs) while retaining the flexibility to be reconfigured as requirements change. 

A number of companies including AMD (via Xilinx) and Altera produce FPGA-based accelerator cards that plug into standard server PCI Express slots. While FPGAs make for very fast parallel accelerators, they are typically more difficult to program than CPU or GPUs.   

### QPU

A Quantum Processing Unit (QPU) is the quantum computing counterpart to a traditional CPU. Instead of using binary bits, it operates with quantum bits (qubits), leveraging principles of quantum mechanics such as superposition and entanglement to solve certain classes of mathematical and computational problems far more efficiently than conventional CPUs or GPUs.  

Although quantum computing technology has advanced significantly and several production-ready or near-production-ready quantum systems are now available, the field remains in its early stages. As of 2026, QPUs are typically accessed through specialized quantum computing systems or cloud-based services, rather than as widely available accelerator cards comparable to today's GPU accelerators. 

## Different Types of GPUs 

One of the ways to group GPUs is in terms of the target user: 

### Consumer GPUs 
These range from integrated graphics processors to high-end gaming GPUs found in consumer devices such as laptops and desktop PCs. Most are actively cooled using built-in fans and include products such as the AMD Radeon RX, Intel Arc, and NVIDIA GeForce RTX series. 

### Professional/Workstation GPUs 
Typically, discrete GPUs designed for professional workstations and specialised workloads such as medical imaging, computer-aided design (CAD), engineering, and simulation. These GPUs are generally optimised for stability, reliability, and certified software support, sometimes prioritising these attributes over raw performance. They also often offer significantly larger GPU memory configurations than consumer GPUs. Examples include the AMD Radeon Pro, Intel Arc Pro, and NVIDIA RTX professional product lines. 

### Data Centre GPUs 
These GPUs are purpose-built for deployment in server environments within data centres and typically deliver the highest levels of performance, memory capacity, and memory bandwidth. Due to their power, cooling, and form-factor requirements, they often need to be installed in specific vendor-certified server platforms to ensure safe and reliable operation. Examples of data centre GPU families include AMD Instinct, Intel Data Center GPU Max, and NVIDIA’s data centre offerings, such as the Hopper and Blackwell architectures. 

It is important to note that data centre and professional GPUs are typically much more expensive than consumer GPUs for a given level of raw performance, because they are designed and priced for reliability, support, and specific data centre or professional use cases rather than cost‑per‑frame in gaming. 

While some consumer and workstation GPUs may technically operate in server environments, they are not generally designed, certified, or supported for data centre use. In some cases, vendors, including NVIDIA, impose licensing or support restrictions that limit the use of certain consumer-grade GPUs in data centre deployments. 

When choosing GPUs, RIEs and architects consider: 

???+ note "Examples of HPC workloads include:"
    - **Workload type** (AI training vs inference, simulation, visualisation). 
    - **Precision requirements **(e.g. FP32 vs FP16 vs INT8). 
    - **Performance for specific workloads** (including Tensor Core performance for AI workloads) 
    - **Memory needs **and how many GPUs per node. 
    - **Interconnects **(e.g. NVLink, InfiniBand) and latency. 
  
