# Analysis Report

I was focussing only on the tap-token-audit project. Tapioca is a huge codebase. It was my second Defi Protocol I review, after Maia. 


## Approach taken in evaluating the codebase

Before I started I was reading all the developer documents for Tapioca. This was hard, but very important. I spoke with twMatt with Question and Answers, which was very insightful and watched quite some Videos on Tapioca Radio and read blockchain new articles. Without this, I would probably not have found a way to review this protocol.

Then I started to understand the code structure. I was doing a QA with Rektora and continued to ask questions, but from a point on I prefer working alone. 

This time I tried to find issues first in several files and then began to combinate the vulnerabilities for a larger attack, which worked in two scenarios: 
- Steal 2.5M Tap tokens out of the Airdrop Broker
- Get a 50% discount for 3M tapOFT tokens out of an empty singulairty market; depositing only a few thousand USDs

In the Maia protocol I missed creating working POCs so in this Tapioca Contest I proof my findings, which makes my documentation longer harder to read.

I explain my approach on twTap where I did only find a low vulnerability: 

When reviewing a new contract I use an iterative approach to ensure I developed a strong understanding of the code logic and mechanics. This is very chaotic in the first hours. 

I first get an overview of the variables and function names and go over them many times. Then I start to find an overview on the tests, seeing how they interact with the code. 

Mostly at this point somethings stands out that I check closer, in twTAP it was the function claimable. I was seeing that the expired field of the Participation struct was not used and was following this case, just to find out that this is fine not using it in the claimable function but necessary to use in the releaseTap function. To proof this I created already a POC and wrote the basic structure for the vulnerability, all for nothing.

It is a progress of hyptheses and validation - suspected error, no error. The more I understand the contract the better my assumptions get. At one point I know the contract that good that no more things stand out, no aha moments arrive. 

Thats the time when I stop analyzing and move on. If multiple contracts are involved, then this process lasts longer.

This makes me slower but hopefully better.

## Anything that you learned from evaluating the codebase

Well a lot of course, as in all audits. I learned a lot about tokenomics, defi in general, stable coins, staking, deploying and what Arbirum is and how it differs from Ethereum. To see how a protocol that raised several millions and how they created their community of 500 followers and how the founders speak, how news media talk about them and finally how their code looks like was very insightful


## Any comments for the judge to contextualize your findings
This is my forth audit in a row and I will make a break now. My typscript code for the tests contain of a fixture and a test part. The fixture I mostly copied and adapted or stripped down. The test itself I tried to combinate 80% and wrote the last 20% myself. The smart contract was 100% my work, because it reflects my attack.

With the airdrop hack I would need to develop a node.js script with ethers.js which I did not do, because it would have been too much work. This hack depends on the way the AirdropBroker and aoTAP are deployed, therefore I moved as well into the deployment script, which is out of scope, but it was important to form a hypotheses. 

Thank you for working thorugh my code!



### Time spent:
200 hours