+++
date = "2017-06-17T16:07:17+05:30"
draft = true
title = "Android HAL (Hardware Abstraction Layer)"
tags = ["android" , "HAL" , "embedded" ]
+++

# HAL (Hardware Abstraction Layer)

Hardare Abstraction Layer is the software component which abstracts the hardware . In one context  `operating system` is a hardware abstraction layer . In another context we can say a pheripheral driver is a hardware abstraction layer for that pheripheral hardware . Or some time we can use userspace drievers  which does the job of abstracting the hardware . 

Hardware Abstraction Layer in the context of android operating system is a binary blob which will talk to hardware . `HAL` is nothing more than shared `elf` object ! . Most of the hardware pheripherals will have a `HAL` associated with it. For example wifi , camera , display etc.

In normal embedded linux scenario we will talk to linux kernel via system calls, And system calls are kind of standerd interface for the user space programs to talk to the kernel. In android this is not the case , All applications will try to comply with hal layer instead of the system call layer. 

Every HAL module have a well known symbol called `HMI`. Any subusystem / service request a HAL via a function called `hw_get_module()` . hw\_get\_module. This function can be found in `hardware/libhardware` . 

Every hardware module must have a data structure named `HAL_MODULE_INFO_SYM` and the fields of this data structure must begin with `hw_module_t` followed by module specific information.


