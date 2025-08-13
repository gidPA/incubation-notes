The CAN bus **physical layer** defines the actual electrical, timing, and wiring characteristics that let multiple devices (nodes) communicate over the same pair of wires. It’s basically the “wires and voltages” part of CAN — everything below the frame formats and arbitration rules.

---

## 1. **Wiring & Topology**

- **Bus type**: _Differential two-wire_ system (CAN_H and CAN_L).
- **Typical topology**: Linear (trunk) with short _stubs_ to each node.
- **Termination**:
    - Each end of the bus is terminated with a **120 Ω resistor** between CAN_H and CAN_L.
    - This matches the characteristic impedance (~120 Ω) of twisted-pair cable, preventing reflections.
- **Cable**: Shielded or unshielded twisted pair (STP/UTP) to reduce EMI.

![[Pasted image 20250813084358.png]]
---

## 2. **Signaling**

- **Differential signaling**:
    - **Recessive** bit (bit 1): both CAN_H and CAN_L ~2.5 V (small voltage difference, ~0 V).
    - **Dominant** bit (bit 0): CAN_H ≈ 3.5 V, CAN_L ≈ 1.5 V (difference ~2 V).
    - Dominant always overrides recessive on the bus (used in arbitration).
- **Common mode voltage**: ~2.5 V midpoint to ground, allowing ±2 V tolerance.
- **Ground reference**:
    - Ideally a common ground exists between nodes for EMC and safety.
    - CAN tolerates ground shifts of up to about ±2 V without communication failure.

![[Pasted image 20250813084547.png]]

---

## 3. **Voltage & Current Levels**

- **High-speed CAN (ISO 11898-2)**:
    - Dominant differential voltage: 1.5 V to 3 V.
    - Recessive differential voltage: < 0.5 V.
    - Bus load: typically 60 Ω (two 120 Ω in parallel).
- **Low-speed/fault-tolerant CAN (ISO 11898-3)** has slightly different thresholds and recovery features.

---

## 4. **Bit Rates & Cable Length**

Physical layer timing is a tradeoff between bit rate and cable length (propagation delay).  
Typical high-speed CAN limits:

|Bit rate|Max cable length (typical)|
|---|---|
|1 Mbit/s|~40 m|
|500 kbit/s|~100 m|
|250 kbit/s|~250 m|
|125 kbit/s|~500 m|
|50 kbit/s|~1 km|

> Rule of thumb: Longer cables → lower bit rate.

---

## 5. **Transceivers**

- The physical layer is implemented by the **CAN transceiver** chip (e.g., MCP2551, TJA1050, SN65HVD230).
- The transceiver:
    - Converts logic-level TX/RX signals from the CAN controller to differential voltages on the bus.
    - Provides short-circuit protection, thermal shutdown, and bus fault protection.
    - In high-speed CAN, it actively drives dominant bits but lets recessive bits float (via internal resistors).

---

## 6. **Error & Fault Tolerance**

- **Dominant level detection** ensures reliable arbitration.
- Bus fault protection: short to ground, short to battery, or wire break scenarios can be handled differently depending on ISO standard.
- Fault-tolerant CAN (ISO 11898-3) uses separate recessive biasing so that communication continues in single-wire mode if one wire is damaged.

---

## 7. **Key Physical Layer Standards**

- **ISO 11898-2**: High-speed CAN (up to 1 Mbit/s, used in most automotive and industrial systems).
- **ISO 11898-3**: Low-speed, fault-tolerant CAN (up to 125 kbit/s, used in body electronics).
- **ISO 11898-5**: High-speed CAN with low-power standby.
- **ISO 11898-6**: High-speed CAN with selective wake-up.