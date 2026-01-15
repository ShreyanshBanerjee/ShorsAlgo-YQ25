# ShorsAlgo-YQ25
# ShorsAlgo-YQ25
## Introduction 
## Stats
Data collected from the algorithm.
The averages are taken from 15 different runs of the Shor's algorithm factoring N (running only one job)
| N	  | Total Qubits | Avg Attempts |	Average Time |
|-----|--------------|--------------|--------------|
|15	  | 8	           | 13.9	        | 3.80s        |
|143  |	16	         | 4.5          |	1.72s        |
|899  |	20	         | 7.7	        | 2.49s	       |
|3127 | 24	         | 10.8	        |	4.42s	       |
|11009| 28	         | 22.6	        | 8.91s        |
|47053|	32		       | 35.4	        | 14.7s        |

After 47053 it starts to take quite a while to run 15 iterations of Shor's for sucessive semiprimes, so I stopped benchmarking the primes after.
Highest semiprime factored successfully: 11426971 (haven't tested larger numbers for any longer than a few minutes)

## In-Depth Explanation
### Shor's Algorithm Overview
Shor's algorithm is a way to factor a semiprime N (a number that is the product of two primes). It does this in the following steps:
1. Pick a random value A that is coprime to N (meaning it shares no factors with N besides one)
2. Construct the function f(x)=a<sup>x</sup> mod N
3. Plug in multiple values of x into f(x) until you find a repeating value. Set `r` to the distance between repeating values (also known as the period)
4. If r is odd, go back to step 1. Else, evaluate GCD(a<sup>0.5r</sup>+1, N) and GCD(a<sup>0.5r</sup>-1, N)
5. If either values aren't one, you've found the prime factors of N. Else, go back to step 1.

Now, on any classical computer (with bits that can only be 1 or 0), this is not the most efficient method. However, quantum computers let us leverage special effects that allow for the above method to be greatly sped up.
## What is Quantum Computing
On a normal computer, there are **bits**, small electrical switches that are either off (0) or on (1). Bits are used to store everything, including instructions and numbers (via binary). The computers we use every single day are called **classical computers**. However, **quantum computers** are made of **qubits**, or bits that can be **both 0 and 1**, or off and on. This is called **superposition**. Qubits that are in superposition don't have a definite value, but when they are **measured**, the superposition collapses into either off or on. Superposition can be leveraged to try out multiple inputs at the same time. The second key advantage that quantum computers have is **entanglement**, which is when the outcome of a qubit depends on another qubit. Let's break this down with a scenario:  

We have two qubits, A and B. Gate A is in a superposition of both 0 and 1, while gate B is just 0.
We then apply a CONTROLLED NOT gate. This gate inverts the second value **if and only if** the first value is 1.
The controlled not gate (or CX) looks at qubit A and modifies qubit B.  
But since A is in a superposition, the value of B depends on what the superposition of A collapses into - entanglement.
If we measure A to be 0, we already know B is 0. Conversely, if we measure A to be 1, B is definitely 1 as well.  
Entanglement, like superposition, are key elements of Shor's algorithm which allow it to perform so quickly.
### Quantum Implementation of Shor's Algorithm
When Shor's algorithm is used in a quantum context, things are a little different. We need to be able to operate on a register that is in superposition.
1. On a classical computer, a random number is generated in the range [2, N-1]. We ensure that this number is coprime respective to N. This is our a value.
2. Secondly, we initialize two quantum registers, the control register and working register. Also, we initialize an output register composed of classical bits in which the output will be read from.
3. We put control and working registers into superposition by applying the HADAMARD gate to each qubit.
4. Then, we perform controlled modular exponentiation, using the working gate as the exponent. The controlled gate looks at the control register, which is in superposition currently, causing entanglement between both registers.
5. Now that we've also performed modular exponentiation, the working gate contains periodic values of the function f(x)=a<sup>x</sup> mod N. The last quantum operation is performing inverse Quantum Fourier Transform, which gives us a high probability of extracting r, the period.
6. Then, we measure the register and convert it from binary into base10.
7. We are almost finished. The result from all of this is k/r, where k and r are both integers. We need to get r, so we approximate the fraction. Now we have r. If r is odd, we need to repeat the precoess with a different A. Else, we perform GCD(a<sup>r</sup>+1, N) and  GCD(a<sup>r</sup>-1, N).
8. If either result is 1, we've gotten 1 x N instead and need to repeat the process. Otherwise, both values are the proper factors of N!

It sounds like a lot, but it was truly easier to implement than it seems. Also, the QuantumRings backend for the algorithm greatly helped, as it allowed to simulate many more qubits than possible (other software allows simulation of ~35 qubits depending on computer, QuantumRings backend allows up to 200!), as without it we would not be able to factor larger numbers.
