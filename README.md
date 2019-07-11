# DPTP
Data-Plane Time synchronization Protocol

# Topology 
The Topology used in as below : 
![DPTP Topology](Tofino-minibed-timesync.png)


A single tofino switch named "tofino1" is virtualized into two switches Master(M) and Switch1. To do this virtualization, you will need to add a loopback link between port3 (160-163) and port5 (176-179). Once done, it will be configured as 10G ports, and we will be using only one link (160-176) as the connection between Switch1 and Master. Additionally, you will need atleast one host connected to port 1(128-131) to send DPTP requests.
### Steps to run DPTP in Tofino:

1) Navigate to the SDE PATH :
```shell
     cd ~/bf-sde-8.x.x
```
2) Set the env variables : 
```shell
     . ./set_sde.bash
```
3) Build the p4 program using the command :
```shell
     ./p4_build ../<YOUR PATH>/DPTP/dptp.p4
```
4) Load the p4 program, and run the control plane API code using :
```shell
     "cd ../<YOUR PATH>/CP"
     "./run.sh"
```
5) This should automatically start the synchronization between Switch1 and master through packets from control-plane.

### Steps to run MoonGen for host synchronization:
Moongen script sends synchronization requests packets between switches
Pull from https://github.com/praveingk/moongen/, Make sure the submodule libmoon is also pulled. 
Follow the readme instructions in moongen to build it.
1) Enable the NIC to work with DPDK:
```shell
sudo ./libmoon/deps/dpdk/usertools/dpdk-devbind.py --b igb_uio <NIC Port>
```

2) To start DPTP in the network between switch 1 and master:
```shell
 sudo ./build/MoonGen examples/dptp_topo.lua <DPDK PORT id1> <DPDK Port id2>
```

Note that "DPDK Port id2" is redundant and is used only for switch-to-host DPTP. 
For just switch-to-switch DPTP, the command could be :
```shell
 sudo ./build/MoonGen examples/dptp_topo.lua 0 0
```
Now, you must be seeing prints on the Switches along with the synchronization accuracy.