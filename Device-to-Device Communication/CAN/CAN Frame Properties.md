The ISO-11898:2003 Standard, with the **standard** 11-bit identifier, provides for signaling rates from 125 kbps to 1 Mbps. The standard was later amended with the “**extended**” 29-bit identifier.
### **Standard CAN**

![Standard CAN frame data diagram](https://automotivevehicletesting.com/wp-content/uploads/2024/05/Standard-CAN-frame-structure-1024x382.png)

- **SOF**
  The single dominant **start of frame** (SOF) bit marks the start of a message, and is used to synchronize the nodes on a bus after being idle.
- **Identifier**
  The Standard CAN 11-bit identifier establishes the priority of the message. The lower the binary value, the higher its priority.
- **RTR**
  The single remote **transmission request** (RTR) bit is dominant when information is required from another node. All nodes receive the request, but the identifier determines the specified node. The responding data is also received by all nodes and used by any node interested. In this way, all data being used in a system is uniform.
- **IDE**
  A dominant single **identifier extension** (IDE) bit means that a standard CAN identifier with no extension is being transmitted.
- **r0**
  Reserved bit (for possible use by future standard amendment).
- **DLC**
  The 4-bit **data length code** (DLC) contains the number of bytes of data being transmitted.
- **Data**
  Up to 64 bits of application data may be transmitted.
- **CRC**
  The 16-bit (15 bits plus **delimiter** (DEL)) **cyclic redundancy check** (CRC) contains the checksum (number of bits transmitted) of the preceding application data for error detection.
- **ACK**
  Every node receiving an accurate message overwrites this recessive bit in the original message with a dominate bit, indicating an error-free message has been sent. Should a receiving node detect an error and leave this bit recessive, it discards the message and the sending node repeats the message after rearbitration. In this way, each node **acknowledges** (ACK) the integrity of its data. ACK is 2 bits, one is the acknowledgment bit and the second is a **delimiter** (DEL).
- **EOF**
  This **end-of-frame** (EOF), 7-bit field marks the end of a CAN frame (message) and disables bit-stuffing, indicating a stuffing error when dominant. When 5 bits of the same logic level occur in succession during normal operation, a bit of the opposite logic level is stuffed into the data.
- **IFS**–This 7-bit **interframe space** (IFS) contains the time required by the controller to move a correctly received frame to its proper position in a message buffer area.

### **Extended CAN**

![](https://automotivevehicletesting.com/wp-content/uploads/2024/05/Extended-CAN-frame-structure-1024x384.png)

The Extended CAN message is the same as the Standard message with the addition of:
- **SRR**
  The **substitute remote request** (SRR) bit replaces the RTR bit in the standard message location as a placeholder in the extended format.
- **IDE**
  A recessive bit in the **identifier extension** (IDE) indicates that more identifier bits follow. The 18-bit extension follows IDE.
- **r1**
  Following the RTR and r0 bits, an additional reserve bit has been included ahead of the DLC bit.