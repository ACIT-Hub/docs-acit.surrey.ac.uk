# HPC and Accelerated Compute

High-performance computing (HPC) is about using many computing resources together to solve problems that are too large, too complex, or too slow for a single normal computer. 
???+ note "Examples of HPC workloads include:"

    - Climate and weather simulations. 
    - Large‑scale molecular dynamics. 
    - Training big AI/ML models. 
    - Processing huge datasets (e.g. genomics, imaging). 



## 1. Multi-CPU and Multi-core

Initially, before multi-core systems had been invented, if you wanted more compute power, you added more CPUs. 

Here's an example of a multi-CPU motherboard: 


<div class="img-box" markdown>
![Logo](../../assets/images/mutli-cpu-multi-core.png){ .img-left }


</div>
*Figure 1: Source: [Phoronix](https://www.phoronix.com/news/Arch-Linux-Deprecating-i686)*

CPUs communicated via a high-speed interconnect allowing for all the CPUs to share the pool of resources. This laid the foundation for development of multi-core CPUs. 

Most physical CPU chips have multiple sub-CPUs, called ‘cores’. 

This CPU has 4 cores: 

<div class="img-box" markdown>
![Logo](../../assets/images/cpu4cores.png){ .img-left width="300" }


</div>
*Figure 1.1: Source:*

This means that it can do four things at once. 

If we want to add two lists of numbers together, we need a bit of the hardware to do the job. 

To add these lists together we can start by adding 4 and 7. Let’s send them to the green CPU core: 



<div class="img-box" markdown>
![Logo](../../assets/images/4and7image.png){ .img-left width="500" }


</div>
*Figure 1.2: Source:*

Let’s send other calculations to them: 



Now the green core is busy doing the calculation, but the other three cores can be doing work at the same time. 

Let’s send other calculations to them: 

<div class="img-box" markdown>
![Logo](../../assets/images/greencore.png){ .img-left width="500"}


</div>
*Figure 1.3: Source:*


The four cores are all now busy doing work. 

There’s always a little bit of variation so some may finish their work earlier than others. 
As soon as one core has finished its first piece of work, we can send over the next bit: 

<div class="img-box" markdown>
![Logo](../../assets/images/4busycores.png){ .img-left width="500"}


</div>
*Figure 1.4: Source:*


You will notice that it’s not exactly four times faster, but it is faster. 

The challenge with using multi-core programming is: 

- Designing the problem so that it can be broken down into chunks like this. 
- Avoiding one of the cores messing with data that’s being used by another core. 


## 2. Multi-node

Content...

## 3. Vector Extensions

Content...

## 4. Exercises/MCQ's

Content...