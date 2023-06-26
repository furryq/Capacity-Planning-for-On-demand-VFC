# Capacity-Planning-for-On-demand-VFC

This is the code for On-demand Vehicular Fog Computing for Beyond 5G Networks. You can cite us by:

W. Mao, O.U.Akgul, B. Cho, Y. Xiao, and A. Ylä-Jääski, “On-demand Vehicular Fog Computing for Beyond 5G Networks”, IEEE Transactions on Vehicular Technology (accepted for publication). 2023.

Pre-print version: https://www.techrxiv.org/articles/preprint/On-demand_Vehicular_Fog_Computing_for_Beyond_5G_Networks/19794472/1


# Senario
Fig. 1a illustrates an exemplary scenario of on-demand vehicular fog computing (ODVFC) that we envision. In this work, a city is divided into multiple connected but independent regions, each consisting of multiple nodes. The nodes are the start or end points of the road segments that compose the road. 

In the region shown in the figure, two cellular cells are deployed along a road that consists of at least five nodes. There are five users in the figure. Based on the workload estimation, three VFNs have been allocated to this region beforehand. At the upcoming time slot, User A and User B will arrive at the purple and red nodes, respectively, both are located within the communication range of Cell 1. Thus VFN 1, whose capacity is high enough to serve both User A and User B, is sent to the purple node. Similarly, we route VFN 2 and VFN 3 to the yellow node, so that they can serve Users C, D, and E. 

To ensure the availability of a VFN in a given cell when a latency-critical service is received, we assume that VFNs are routed to the regions before the demand is generated. To fulfill this assumption, it is essential to be able to accurately predict the traffic flow and the corresponding demand for computational resources in the next moment or time window. In the case of ODVFC, the capacity plan needs to be updated frequently, which requires the capacity planning solution to be lightweight enough to calculate the routes within minutes or even seconds. 

For ODVFC, we assume that communication between vehicles is implemented with 5G new radio (NR) V2X. In this work, we assume that the vehicles communicate with others connected to the same cell using 5G V2N.

![image](https://github.com/furryq/Capacity-Planning-for-On-demand-VFC/assets/61701502/b8a33fdb-fa2b-4c22-afe2-16d48db9babf)
Fig. 1: Overview of ODVFC. (a) Exemplary scenario, (b) Time horizon, and (c) Flowchart of ODCP.

# Capacity Planning Framework 
Such an ODVFC scenario can be envisioned where we route the VFNs to places where new demand emerges. Unlike commercial fleets, the VFNs in ODVFC are installed on taxis or other vehicles with flexible schedules and adaptable routes. In this paper, we target capacity planning in ODVFC, which is a challenging problem. Due to the Bellman called curse of dimensionality, the computational time of the capacity planning model increases with the number of tasks and VFNs. Unlike long-term planning which allows sufficient time (i.e., days or weeks) to find the optimal solution, ODVFC is considered as short-term capacity planning that requires the solution to be found in minutes or even seconds.

To address the computation time limit, we propose an integer linear programming (ILP) based framework referred to as on-demand capacity planning (ODCP) to create the capacity plan in two steps.
During global planning, we determine the global routing strategy for VFNs. In this way, the service rate is maximized at the city scale with the overall capacity constraint. Meanwhile, the VFNs are routed to the corresponding regions with minimized traveling costs. 
During regional planning, we decide the task allocation and regional routing strategies for VFNs. More specifically, the VFNs are routed according to the locations of the users within the same region with minimized costs. Meanwhile, the VFNs are assigned to the users within the communication range with minimized service level agreement (SLA) violations. Such two-phase capacity planning enables parallel decision-making at the regional level, which greatly shortens the computational time. 

The aim of ODCP is to maximize the profit of the service provider and the quality of service (QoS) received by the users. We follow a data-driven methodology to implement capacity planning in ODVFC. The flowchart of ODCP is presented in Fig. 1c, where the demand and supply are modeled separately. 

# Code
The code consists of xx parts. 

# Simulator
To evaluate the proposed capacity plan, we proposed a vehicular fog computing (VFC) simulator, referred to as VFogSim. You can cite us by:

U. Akgul, W. Mao, B. Cho, and Y. Xiao, "VFogSim: A Data-driven Platform for Simulating Vehicular Fog Computing Environment," IEEE Systems Journal (accepted for publication). 2023.  

Link to the simulator: https://mobilecloud.aalto.fi/?page_id=1441 (There are both Matlab and OMNeT++ versions, the future version will be combined with Ray RLlib)

Preprint version: https://www.techrxiv.org/articles/preprint/A_Data-driven_Platform_for_Simulating_Vehicular_Fog_Computing_Environment/17829398/2

