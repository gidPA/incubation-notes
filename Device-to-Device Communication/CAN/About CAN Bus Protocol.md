CAN bus stands for Controller Area Network bus. It is a vehicle bus standard designed to enable efficient communications primarily between electronic control units (ECUs).

The CAN bus was developed by BOSCH (1) as a **multi-master, message broadcast system** that specifies a maximum signaling rate of **1 megabit per second** (bps). Unlike a traditional network such as USB or Ethernet, CAN does not send large blocks of data point-to-point from node A to node B under the supervision of a central bus master. In a CAN network, many short messages like temperature or RPM are **broadcast to the entire network**, which provides for data consistency in every node of the system.

CAN is an International Standardization Organization (ISO) defined serial communications bus originally developed for the automotive industry to replace the complex wiring harness with a two-wire bus. The specification calls for high immunity to electrical interference and the ability to self-diagnose and repair data errors. These features have led to CAN’s popularity in a variety of industries including building automation, medical, and manufacturing.

The CAN communications protocol, ISO-11898: 2003, describes how information is passed between devices on a network and conforms to the Open Systems Interconnection (OSI) model that is defined in terms of layers. Actual communication between devices connected by the physical medium is defined by the **physical layer** of the model. 

The ISO 11898 architecture defines the lowest two layers of the seven layer OSI/ISO model as the data-link layer and physical layer, as seen in the figure below:

![[Pasted image 20250813083900.png]]

The CAN communication protocol is a carrier-sense, multiple-access protocol with collision detection and arbitration on message priority (CSMA/CD+AMP).
- CSMA means that each node on a bus must wait for a prescribed period of inactivity before attempting to send a message. 
- CD+AMP means that collisions are resolved through a bit-wise arbitration, based on a preprogrammed priority of each message in the identifier field of a message. The higher priority identifier always wins bus access. That is, the last logic-high in the identifier keeps on transmitting because it is the highest priority.

Since every node on a bus takes part in writing every bit "as it is being written," an arbitrating node knows if it placed the logic-high bit on the bus.

The [[CAN Frame Properties|ISO-11898:2003 Standard]], with the standard 11-bit identifier, provides for signaling rates from 125 kbps to 1 Mbps. The standard was later amended with the “extended” 29-bit identifier.