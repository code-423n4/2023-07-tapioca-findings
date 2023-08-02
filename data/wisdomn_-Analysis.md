- Approach taken in evaluating the codebase.

I followed this approach
Critical Contracts - Critical Functions - Critical Parts

I looked out for critical points like when the function makes external calls to other contracts (.transfer(), .withdraw(), .deposit()) to prevent Reentrancy Attacks.

I also looked out for critical points that needed authorization and lacked it to prevent Access Control Attack. 

- Anything that you learned from evaluating the codebase

Functions that are repeatedly used across many contracts should be monitored closely (e.g. Withdraw(), Deposit(), etc.). 
Large codebase are more prone to vulnerabilities (and repeated vulnerabilities as stated above).

- Any comments for the judge to contextualize your findings
I included permalinks as instructed to help you locate the functions quickly. 

### Time spent:
80 hours