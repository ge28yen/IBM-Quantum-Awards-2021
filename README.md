# IBM-Quantum-Awards-2021 -  Optimizing Controlled-Phase Interaction with Qiskit Pulse

Qiskit-Pulse-based solution for the IBM Quantum Awards 2021, see: \
https://ibmquantumawards.bemyapp.com/#/event \

In the attached jupyter notebook I explain in detail how I approached the challenge. \
Due to very low accessability of the IBM Jakarta system, the solution has not yet been run.\
This might be the case for anyone who tries to optimize pulses on this system.

The solution was mostly inspired by : \
B. K. Mitchell, R. K. Naik, A. Morvan, A. Hashim, J. M. Kreikebaum, B. Marinelli, W. Lavrijsen, K. Nowrouzi, D. I. Santiago, and I. Siddiqi Phys. Rev. Lett. 127, 200502(2021)

## The Problem

The objective of the IBM Open Science Prize was to increase the fidelity of simulation of the Heisenberg Hamiltonian on the IBM Jakarta backend. 
The Heisenberg model of ferromagnetism is defined through the following Hamiltonian:

$$
\mathcal{H} = -\sum_{i, j} \mathcal{J}_{ij} \vec{s}_i \cdot \vec{s}_j
$$

where

$$
\mathcal{J}_{ij} = 
\begin{cases} 
J & \text{if } i,j \text{ are neighbors} \\ 
0 & \text{else}
\end{cases}
$$

is a coupling between spins. 
In this case, the objective was to simulate a chain of five spins. The results would be evaluated based on the evolution of the quantum state under the given Hamiltonian. 

## Introduction
I approached the problem by assuming the trotteriaztion of steps, and optimizing the  controlled phase (ZZ) interaction using Qiskit Pulse. This interactions is often hardware-efficient in interactions such as the Cross-Resonance interaction, and can be easily translated into XX and YY operations with single-qubit gates. Additional phase shifts were applied on both qubits, which is the keystone of the algorithm. In the case of 4 Trotter steps, this interaction is employed 24 times.

## First Approach: Using the CR Gate
### References
1. S. Sheldon, E. Magesan, J. M. Chow, J. M. Gambetta, Phys. Rev. A 93, 060302(R) (2016
2. N. Sundaresan, I. Lauer, E. Pritchett, E. Magesan, P. Jurcevic, J. M. Gambetta, PRX Quantum 1, 020318 (2020)

### Method
The idea was to use the CR Gate while trying to maximize the $ZZ$ interaction rate. It was possible to retain the original $ZX$ interaction and perform the $CX*IZ*CX$ sequence while also inducing $ZZ$ interaction during Cross-Resonance. This would speed up the overall $CX*IZ*CX$ sequence, leading to improved fidelity.

The available degrees of freedom were:
- Control amplitude
- Control phase
- Overall signal length
- Rotary rate

### Results
The experiments were performed on the IBMq Santiago System, as the Jakarta system was unavailable for a long time. The results were unsatisfactory, as no ZZ gate fidelity above 0.4 could be reproduced.

## Second Approach: Hardware-Efficient ZZ Gate
### Reference
- B. K. Mitchell, R. K. Naik, A. Morvan, A. Hashim, J. M. Kreikebaum, B. Marinelli, W. Lavrijsen, K. Nowrouzi, D. I. Santiago, and I. Siddiqi, Phys. Rev. Lett. 127, 200502 (2021)

### Method
It is possible to activate the ZZ interaction by driving both coupled, fixed-frequency qubits at a frequency between the $|1> -> |2>$ transition of the higher-frequency qubit and the $|0> -> |1>$ transition of the lower-frequency qubit. However, the exact driving frequency, pulse amplitude, phase, pulse duration, and IZ and ZI corrections have to be optimized to achieve a high fidelity gate with the desired conditional phase change.

### Challenges
IBM Jakarta access was granted late, and a single job waiting time was about a week. Other backends do not support advanced Qiskit Pulse functionalities. Despite high effort, the later part of the code could not be tested and may contain bugs.

## Implementation Steps
See provided Jupyter Notebook for details.
### Step 1: Import Necessary Packages and Load Jakarta Backend

### Step 2: Find Qubit Frequencies
Identify the frequencies of qubits 1, 3, and 5 on the Jakarta system.

### Step 3: Optimize ZZ Interaction for Qubits 1 and 3
- Perform parameter sweeps and retain the value resulting in the highest fidelity.
- Conduct a refined frequency sweep around the best frequency with a smaller step size.
- Use Hamiltonian process tomography to identify gate fidelity.

### Step 4: Find Optimal Pulse Length
- Define parameter sweeps for pulse length optimization.
- Run Hamiltonian tomography to find the pulse length that matches the desired conditional phase shift.

### Step 5: Repeat Optimization for Qubits 3 and 5

### Step 6: Build the Final Trotter Step Using Optimized ZZ Gate Parameters
- Construct the Trotter step with the optimized parameters.
- Append the Trotter gate to the quantum circuit.

### Step 7: Simulate Time Evolution and Evaluate Fidelity
- Prepare the initial state.
- Simulate time evolution under the H_heis3 Hamiltonian.
- Evaluate the simulation at the target time.
- Generate state tomography circuits and evaluate fidelity.

### Conclusion
The implemented approach optimized the controlled-phase interaction using Qiskit Pulse, resulting in improved gate fidelity. Despite some challenges with backend availability, the optimization process provided a significant improvement over the initial attempts.
