# ShorsAlgo-YQ25
# ShorsAlgo-YQ25
## In-Depth Explanation
### Shor's Algorithm Overview
Shor's algorithm is a method to factor a semiprime N - a number that is the product of two primes.
The algorithm can be broken into the following steps: 
1. Pick a random value A that is coprime to N (meaning it shares no factors with N besides one)
2. Construct the function f(x)=a<sup>x</sup> mod N
3. Plug in multiple values of x into f(x) until you find a repeating value. Set `r` to the distance between repeating values (also known as the period)
4. If r is odd, go back to step 1. Else, evaluate GCD(a<sup>0.5r</sup>+1, N) and GCD(a<sup>0.5r</sup>-1, N)
5. If either values aren't one, you've found the prime factors of N. Else, go back to step 1.

On a classical computer, this is extremely inefficient and would take a long time to factor; however, we can leverage superposition to explore multiple states (speeding up step #2) and interference to eliminate incorrect results.
 
### Quantum Implementation of Shor's Algorithm
1. On a classical computer, a random number is generated in the range [2, N-1]. We ensure that this number is coprime respective to N. This is our a value.
2. Secondly, we initialize two quantum registers, the control register and working register. Also, we initialize an output register composed of classical bits in which the output will be read from.
3. We put control and working registers into superposition by applying the HADAMARD gate to each qubit.
4. Then, we perform controlled modular exponentiation, using the working gate as the exponent. The controlled gate looks at the control register, which is in superposition currently, causing entanglement between both registers.
5. Now that we've also performed modular exponentiation, the working gate contains periodic values of the function f(x)=a<sup>x</sup> mod N. The last quantum operation is performing inverse Quantum Fourier Transform, which gives us a high probability of extracting r, the period.
6. Then, we measure the register and convert it from binary into base10.
7. We are almost finished. The result from all of this is k/r, where k and r are both integers. We need to get r, so we approximate the fraction. Now we have r. If r is odd, we need to repeat the precoess with a different A. Else, we perform GCD(a<sup>r</sup>+1, N) and  GCD(a<sup>r</sup>-1, N).
8. If either result is 1, we've gotten 1 x N instead and need to repeat the process. Otherwise, both values are the proper factors of N!
