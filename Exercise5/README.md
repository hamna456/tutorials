## Exercise 5 - PINS Fabric Demonstration

PINS launched at the OCP Summit. If you have not already watched the [complete vide](https://www.youtube.com/watch?v=iZuWdiV9dnc)o of the demonstration, here is a link that jumps to the beginning of the [Weighted Cost MultiPath (WCMP) Demo](https://www.youtube.com/watch?v=iZuWdiV9dnc&t=271s) (6:18). 

This demonstration brings P4 and SDN to SONiC, using ONOS for a control plane to manage routes and set up the flow objectives. The pure SDN solution uses a multi-purpose L2/L3 leaf-spine switching fabric without traditional control protocols such as BGP.  The SDN Controller (ONOS) is decoupled from the data plane. In addition, the spines use L3 segment routing instead of MPLS. The result is that the fabric acts as one big router with P4 support in a SAI instantiation. For more information, explore the [Azure/sonic-pins/sai_p4](https://github.com/Azure/sonic-pins/tree/main/sai_p4/instantiations/google) repository. The `middleblock.p4` instantiation is close to what we used for our demonstration.

The PINS demonstration topology is a 2x2 leaf-spine fabric and server(s) with the following setup:



* 4 PINS switches connected in a 2x2 leaf-spine topology
    * _Spine_: Arista 7060DX4-C32, Broadcom Tomahawk3
    * _Spine_: Accton Wedge100BF-32X, Intel Barefoot Tofino
    * _Leaf_: Arista 7060DX4-C32, Broadcom Tomahawk3
    * _Leaf_: Arista 7170-64C, Intel Barefoot Tofino
* Server: 
    * A server must act as a router to provide connectivity to an external network (i.e., Internet). 
    * A server (could be a laptop) must run ONOS with access to the management network of the switches.
    * Hosts are on the two leaf devices. Instead of physical hosts, network namespaces or mac vlan interfaces can emulate hosts on a server. We used namespeaces in our demo.


### Figure 2 - PINS Demonstration Topology

![alt_text](PINS-Demo-Topology.png "PINS Demo Topology")



### Create a PINS Demonstration on Your Network (Advanced)

If you have access to four switches to create this demonstration, you can download the PINS demonstration software and experiment on your network.

WCMP depends on your network topology. In the future, we will have tools such as gNMI to determine route weights and enable WCMP. For now, you have to choose the weights manually, as we did in the demonstration. Then use the config files found in the demonstration software to achieve WCMP in your network. 

There are five folders containing configuration files in this exercise, one for each switch/ASIC combination and one for ONOS. Each switch needs a `config_db.json` file. The switches that contain a Broadcom ASIC also require `port_config.ini` and `*.config.bcm` to implement SAI.



1. `spine-arista-TH3`: Arista 7060DX4-C32, Broadcom Tomahawk3
2. `spine-accton-BF`: Accton Wedge100BF-32X, Intel Barefoot Tofino
3. `leaf-arista-TH3`: Arista 7060DX4-C32, Broadcom Tomahawk3
4. `leaf-arista-BF`: Arista 7170-64C, Intel Barefoot Tofino
5. `onos-topology`: ONOS configuration file, `netconfig.json`, for segment routing and gRPC connections (p4 runtime connections)

The docker container with ONOS that you got in Exercise 4 has the PINS driver and SAI pipeliner installed. There are two pipelines in `org.onosproject.pipelines.sai` and `org.onosproject.pipelines.sai_fixed`. The latter does not include ACLs. If your topology has other requirements and you need to create other pipelines, explore the [SONiC ONOS Driver](https://github.com/pins/sonic-onos-driver) repository, especially the [loader](https://github.com/pins/sonic-onos-driver/blob/main/pipeliner/src/main/java/org/onosproject/pipelines/sai/SaiPipeconfLoader.java). You will also need follow the instructions in [Build ONOS with PINS](../BuildONOSwithPINS) instead of using the docker image we built for you in Exercise 4.
