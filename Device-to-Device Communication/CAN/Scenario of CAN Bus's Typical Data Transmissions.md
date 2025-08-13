
A typical CAN bus data transmission follows a very structured scenario — it’s not like UART where one device just “starts talking” at any time. CAN uses a shared differential bus with a _message-based_ protocol and built-in arbitration so multiple nodes can communicate without collisions.

The typical scenario of a data transmission from a node is as follows:

---

### **1. Bus Idle**

- The bus is in a **recessive state** (both lines nearly equal voltage, ~2.5 V) when no one is transmitting.
- All nodes are constantly monitoring the bus.

---

### **2. Message Transmission Request**

- A node (ECU, sensor, controller, etc.) has data to send and waits until the bus is idle.
- When idle, it starts sending the **start-of-frame (SOF)** bit, which is **dominant** (logic 0).

---

### **3. Arbitration Phase**

- Multiple nodes might try to send at the same time.
- Each message has an **identifier** (ID) — lower binary value = higher priority.
- CAN uses **bitwise arbitration**:
    - All transmitters monitor the bus while sending.
    - If a node sends recessive (1) but reads dominant (0), it stops transmitting — meaning a higher-priority message is on the bus.
    - The highest-priority node continues without delay.

- An example of arbitration stage can be seen in the timing diagram below:
  
  ![](https://www.researchgate.net/publication/221582662/figure/fig2/AS:852943515418624@1580368989905/CAN-message-top-and-priority-arbitration-example-bottom.ppm)
- **Bit 10 to Bit 6**: All three nodes match the bus level → no one loses yet.
- **Bit 5**:
	- Node 2 sends recessive (1), but Node 1 and Node 3 send dominant (0), so Node 2 loses arbitration and stops transmitting (red section indicates losing).
- **Bit 3**:
	- Node 1 sends recessive (1), but Node 3 sends dominant (0), so Node 1 loses arbitration.
- **After Arbitration**:
	- Node 3 has the lowest ID and wins arbitration.
	- Node 3 continues transmitting its Control Field and Data Field.
	- The losing nodes (Node 1 and Node 2) become receivers.

---

### **4. Data Frame Transmission**

- The winning node sends:
    1. **Arbitration Field** (ID + RTR bit)
    2. **Control Field** (data length code — DLC)
    3. **Data Field** (0–8 bytes for Classical CAN, up to 64 bytes for CAN FD)
    4. **CRC Field** (error check)
    5. **ACK Field** (receiving nodes assert dominant ACK bit if CRC passes)
    6. **End of Frame** (EOF)

---

### **5. Reception and Acknowledgment**

- All other nodes receive the frame.
- If received correctly (no CRC error), they send a **dominant ACK** during the ACK slot.
- If no node ACKs, the transmitter knows the message was not received and will retry.

---

### **6. Error Handling (if needed)**

- CAN has multiple error detection mechanisms:
    - **Bit errors**, **stuff errors**, **CRC errors**, **form errors**, **ack errors**.
- Faulty nodes can enter **error passive** or **bus-off** states to avoid disturbing the network.

---

### **7. Bus Returns to Idle**

- After the **intermission period** (a few recessive bits), the bus is considered idle again.
- Other nodes can attempt transmission immediately — with the same arbitration process.