# Capacity-Planning-for-On-demand-VFC

This is the code for On-demand Vehicular Fog Computing for Beyond 5G Networks. 

If you are interested in this work, you can cite us by:

W. Mao, O. U. Akgul, B. Cho, Y. Xiao and A. Ylä-Jääski, "On-demand Vehicular Fog Computing for Beyond 5G Networks," in IEEE Transactions on Vehicular Technology, doi: 10.1109/TVT.2023.3289862.


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

We generate the road network in Helsinki according to the HERE map (Fig. 2). Based on the road network, the traffic dataset is generated using the Simulation of Urban MObility (SUMO). We generate the microscopic traffic flow (i.e., the location of each user in each ST) following the approach of activity-based demand generation, which creates the trips of the individual vehicles based on the description of the city population. The necessary SUMO files are in the root directory. Particularly, the city description can be checked from here.stat.xml, and the SUMO configuration file is here.sumocfg. Then, we use batch_sumo_traces_10d.ipynb to generate the vehicular traces in 10 days, where we use 9 days to train and the last day to test. 

![segments](https://github.com/furryq/Capacity-Planning-for-On-demand-VFC/assets/61701502/7498061d-2551-466f-88ed-e809102fc509)

Fig. 2: Road network in Helsinki.

The code consists of three packages, namely here_steady, here_seasonal, and here_occassion. They are corresponding to three traffic models:

• Stationary Traffic Model (STM): The traffic pattern remains unchanged during the ten days.

• Traffic Model with a Seasonal Change (TM-SC): There is a gradual change in the distribution of the working hours (cf. Fig. 2) due to a more flexible schedule, such as the hybrid working schedule during the pandemic period.

• Traffic Model with an Occasional Event (TM-OE): There is a sudden change in the demand magnitude (i.e., the total number of users doubled from 5000 to 10000) over
the last four days due to an occasional event, such as an international football match.

![working_hour_1](https://github.com/furryq/Capacity-Planning-for-On-demand-VFC/assets/61701502/ce808c3f-5e61-4ce1-9973-a6e20ccb79fe)
![working_hour_2](https://github.com/furryq/Capacity-Planning-for-On-demand-VFC/assets/61701502/a4ee8cef-7444-42ad-a87b-cdb561c88100)
![working_hour_3](https://github.com/furryq/Capacity-Planning-for-On-demand-VFC/assets/61701502/2e06d492-4c32-4e93-b367-aaa806bc03c1)

Fig. 2: Working time transition in TM-SC.

Due to the uncertainty in traffic flow, the traffic flow prediction cannot always be accurate. The impacts of traffic flow prediction accuracy on capacity planning are analyzed through the following scenarios:

• ODCP with Accurate Traffic (AT): Assume that the traffic flow prediction is 100% accurate and use the proposed model for capacity planning.

• ODCP with Traffic Prediction With Feedback (TP-WF): Predict traffic flow using SARIMA, which takes the prediction errors in the previous time slot as feedback
and uses the proposed model for capacity planning.

• ODCP with Traffic Regression with No Feedback (TR-NF): Estimate the traffic flow based on Gaussian process regression from the historical data and use the proposed model for capacity planning.

TP-WF is our proposal, AT is the baseline, and TR-NF is used for comparison.

We also compare ODCP with the following strategies presented in the literature:

• Vehicle Routing method (VR): Formulate the planning problem as VRPTW [13] and solve it using ILP.

• Randomly Go and serve (RG): The VFNs randomly travel among the regions and serve the demand that is within the same region (i.e., a naive approach).

The association between the codes and scenarios are:
capacity_planning_best - AT
capacity_planning_pre - TP-WF
capacity_planning_old - TR-NF
capacity_planning_all - VR
capacity_planning_random - RG

For the results, cp.profit_list and cp.service_rate_list show the profits and service rates in 24 hours, respectively. cp.times_cs and cp.times_rg_block show the execution time for global and regional planning, respectively.
You can use papermill.ipynb for batch-running the simulations.


# Simulator
To evaluate the capacity plan in a finer granularity, we proposed a vehicular fog computing (VFC) simulator, referred to as VFogSim.

![Architecture](https://github.com/furryq/Capacity-Planning-for-On-demand-VFC/assets/61701502/f6be0e2b-f1a2-48f0-be10-458693b6a679)
Fig. 3: The VFogSim simulator.

If you want to use the simulator, you can cite us by:

U. Akgul, W. Mao, B. Cho, and Y. Xiao, "VFogSim: A Data-driven Platform for Simulating Vehicular Fog Computing Environment," IEEE Systems Journal (accepted for publication). 2023.  

Link to the simulator: https://mobilecloud.aalto.fi/?page_id=1441 (There are both Matlab and OMNeT++ versions, the future version will be combined with Ray RLlib)

Preprint version: https://www.techrxiv.org/articles/preprint/A_Data-driven_Platform_for_Simulating_Vehicular_Fog_Computing_Environment/17829398/2

