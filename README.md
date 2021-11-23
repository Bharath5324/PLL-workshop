#Phase Locked Loop IC design using Google Skywater 130nm Workshop

## What is PLL?
---
PLL or Phase Locked Loop is a circuit arrangement used obtain precise clock signal without frequency or phase noise according to a given reference frequency of our choice. 

<br>
<br>

## Day 1 - PLL Theory and Lab Setup:
---

### **Topic #1:** Introduction to PLL

There are two kinds of oscillators that are used, they are:
1. Voltage Controlled Oscillators - They are oscillators whose frequency can be varied but can be affected by unwanted noises. 
2. Quartz Crystal Oscillators - They have pure spectrum, that is will not have nor get affected any unwanted noises.
   ![Pure Spectrum Vs Noisy Spectrum](./assets/pure-vs-noisy.png)

- PLL intuition
  - PLL's as mentioned above are used to mimic a reference wave, that is to have the same or a multiple of the reference frequency with a constant phase difference.
- Components of a PLL
  ![Pure Spectrum Vs Noisy Spectrum](./assets/components-pll.png)
  
  <br>
  <br>

  - **Phase Frequency Detector(PFD):** Combines the output signal with the reference signal to receive the magnitude and sign of the phase difference between the current output and the reference signal
  - **Voltage Controlled Oscillator:** On-chip oscillator
  - **Charge Pump**: COnverts the digital output received from the PFD to analog
  - **Low Pass Filter:** Smoothens the current pulse in the output.

```Note: Such a multiplier is known as a "Clock Multiplier Phase Locked Loop" ```

<br>


### **Topic #2:** Introduction to Phase Frequency Detector
---
<br>

  - **Aim:** Give an output frequency showing the magnitude and sign of the phase difference between referance signal and output signal. 
  - **Method 1:**
    - Let's consider Doing an XOR operation between ouptut and reference signal
    ![XOR as PFD](./assets/XOR-[PFD-Case-1].png)

    - Here the problem is that we can't understand if the signal is leading or lacking without knowing which the output cannot be tuned accordingly.
  - **Method 2:**
    - In this case, consider two output signals, UP and DOWN
      - UP - if HIGH the output is lagging.
      - DOWN - if HIGH the output is leading.
    ![UP AND DOWN SIGNALS](./assets/leading-lagging.png)

    - Therefore for it's excitation diagram can be expressed as:
      - DOWN: From negative edge of OUT to negative edge of REF
      -  UP: From negative edge of REF to negative edge of OUT
      ![Excitation Diagram](./assets/PFD-Excitation.png)
   - Since the output of the circuit is edge triggered the circuit must be sequential in other words must be made up of flipflops.
   - The circuit diagram will be as follows:
   ![Circuit Diagram](./assets/PFD-Diagram.png)


```Note: When the gap between the triggers are really low the output at both UP and DOWN respectively won't be a complete one but rather a spike. This is known as a dead zone ```

### **Topic #3:** Introduction to Charge Pump
---
<br>
Converts the digital measure of phase/frequency difference into an analog control signal to control signal to control the oscillator.

  - This is achieved using a circuit called the current steering circuit.
  - It's purpose is to steer the current from VDD to output or from output to the ground according to the UP and DOWN signal we provide.
   ![CP Circuit Diagram](./assets/CP-Diagram.png)
  <br>
  - When UP signal is active the current flows from VDD to output capacitor and charges it - increasing the charge at the output
  - If DOWN signal is active then the current flows from ground to the output capacitor hence the voltage at the output decreases thereby discharging the output capacitor
  - The output capacitor helps in smoothening the output signal
  - Therefore it's output characteristics will be

| Case      | Output Graph |
| --------- |  ----------- |
| Avg. UP signal active time >Avg. DOWN signal active time|![CP Output Up Signal](./assets/CP-output-UP.png)|
| Avg. DOWN signal active time >Avg. UP signal active time|![CP Output Down Signal](./assets/CP-output-DOWN.png)|

- Here the increase in voltage speeds up the oscillator while the reduction in current slows it down. 
- The MOSFET circuit for the same is:
  
  ![CP Circuit Diagram](./assets/CP-Circuit.png)

- A small problem with the circuit is, even when both UP and DOWN signals are low, a small current in the form of leakage flows through the circuit. This continuously charges the circuit.

### Loop Filter

- Even after the smoothening of output signal by the output capacitor there is a chance for the output to output
- Without the loop filter the phase locked loop will not be able to lock and mimic the reference signal.
- THe details concerning the loop filter are as the follows:
  ![Loop filter information](assets/Loop-filter.png)

### Voltage COntrolled Oscillator

- Consider delay as the time taken to charge the output. 
- So, if current is greater, the output gets charged faster.
- In this way, we can control over the oscillator frequency.
-  This can be achieved using a current starving mechanism around a ring oscillator

---
 ```Note: A ring oscillator is actually three CMOS inverters connected in series within a closed loop, as shown in the figure```

![Ring Oscillator](assets/ring-oscillator.png)

---

<br>

- On adding the current control circuit to have the frequency selector, we add the following extra elements into the CMOS inverter.[as shown in the below circuit]
![VCO Circuit Diagram](assets/VCO-Diagram.png)