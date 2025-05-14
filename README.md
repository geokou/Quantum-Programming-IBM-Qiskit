# Book Code

from qiskit import *
%matplotlib inline
from qiskit.visualization import plot_histogram

secretnumber = '101001'

# Build a quantum circuit with 6+1 qubits and n bits.
circuit = QuantumCircuit(6+1, 6)

# Apply Hadamard gates to 6 of the 7 qubits to create superposition
circuit.h([0, 1, 2, 3, 4, 5])

# Draw the circuit (only once)
circuit.draw(output='mpl');


circuit.x(6)  # Apply X gate to flip the seventh qubit
circuit.h(6)  # Then a Hadamard gate

# Draw the circuit (only once)
circuit.draw(output='mpl');


# Build the Oracle
# For every 1 in the secret number '101001' apply a CNOT (CX) gate to the qubit and the seventh (no.6) qubit
# Qubits are numbered from top to bottom: 5th=1, 4th=0, 3rd=1, 2nd=0, 1st=0, zeroth=1
circuit.cx(5, 6)
circuit.cx(3, 6)
circuit.cx(0, 6)

# Draw the circuit (only once)
circuit.draw(output='mpl');


# Apply Hadamard gates to 6 of the 7 qubits again. From q0 to q5
circuit.h([0, 1, 2, 3, 4, 5])
circuit.barrier()
# Draw the circuit
circuit.draw(output='mpl');


# The algorithm is completed. We measure the six qubits q0 to q5 and write the results in the classical register
circuit.measure([0, 1, 2, 3, 4, 5], [0, 1, 2, 3, 4, 5])
# Draw the circuit
circuit.draw(output='mpl');



from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator


# Create simulator
simulator = AerSimulator()

# Transpile the circuit for the simulator
compiled_circuit = transpile(circuit, simulator)

# Run the circuit
result = simulator.run(compiled_circuit, shots=1).result()

# Extract measurement results
counts = result.get_counts()

# Display results
print(counts)

# -------------------------
# New Code (Apply Oracle — dynamically from secretnumber)


from qiskit import QuantumCircuit, transpile
from qiskit.visualization import plot_histogram
from qiskit_aer import AerSimulator
%matplotlib inline

# Your secret string (change it freely!)
secretnumber = '111100'

# Create the circuit: 6 qubits for input + 1 ancilla, and 6 classical bits
circuit = QuantumCircuit(6+1, 6)

# Step 1: Put input qubits into superposition
circuit.h([0, 1, 2, 3, 4, 5])

# Step 2: Prepare ancilla (q6) in |-⟩
circuit.x(6)
circuit.h(6)

# Step 3: Apply Oracle — dynamically from secretnumber
for i, bit in enumerate(reversed(secretnumber)):
    if bit == '1':
        circuit.cx(i, 6)

# Step 4: Apply Hadamard again to input qubits
circuit.h([0, 1, 2, 3, 4, 5])
circuit.barrier()

# Step 5: Measure
circuit.measure([0, 1, 2, 3, 4, 5], [0, 1, 2, 3, 4, 5])

# Step 6: Simulate
simulator = AerSimulator()
compiled_circuit = transpile(circuit, simulator)
result = simulator.run(compiled_circuit, shots=1).result()
counts = result.get_counts()

# Step 7: Display result
print("Measured result:", counts);
plot_histogram(counts);
