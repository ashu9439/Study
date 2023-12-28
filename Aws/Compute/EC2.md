# Types :
 https://aws.amazon.com/ec2/instance-types/
    1. General Purpose
         balance of compute, memory and networking resources, and can be used for a variety of diverse workloads.
    2. Compute Optimized
        high performance processors

         batch processing workloads, media transcoding, high performance web servers, high performance computing (HPC), scientific modeling, dedicated gaming servers and ad server engines, machine learning inference and other compute intensive applications
    3. Memory Optimized
         deliver fast performance for workloads that process large data sets in memory

         optimized for EBS(SSD)
    4. Accelerated Computing
        use hardware accelerators, or co-processors, to perform functions, such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs.
    5. Storage Optimized
         high, sequential read and write access to very large data sets on local storage. They are optimized to deliver tens of thousands of low-latency, random I/O operations per second (IOPS) to applications.

***
# for Scaling:
autoscaling group

# for high Availaility and fail recovery :
deploy on several AZs

# pricing Models:
    1. ondemand instances
    2. dedicated Hosts
    3. spot instances
    4. saving plans

# for provisoning EC2 do folloing
    1. choose Operating sys
    2. Netorking details
    3. storage option
    4. security details

