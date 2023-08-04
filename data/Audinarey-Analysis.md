The TapiocaDao project is the largest I have done after MaiaDao, however it was more painstaking considering the low NATSPEC formatting coverage and comments describing contracts, functions and variables.

## APPROACH
Since the codebase was large, it presents a large attack surface as well and also since it interacts with  external protocols like AMMs, some of my time was spent researching to ensure the interactions were done securely and also I spent time understanding the LayerZero protocol itself.
Because the developer comments were absent in most cases, there were instances where I went over the codebase twice or even more.

## Architecture recommendations
Having the contract as separate projects is not a bad idea, however, the TapiocaDao team can consider modularising some fairly functions in most contracts. The reason for this suggestion is that in the event of an emergency, incidence response teams could find it challenging to offer resolutions as fast as it is needed.

## Codebase quality analysis
The codebase starts out good considering the data structure organisation like the ```structs``` used in the contract also cross contract calls were fairly properly implemented but some vulnerabilities were caused by developer oversight which I assume were due to the complexity of the project.

## Centralization risks
Ownership was properly managed across board in the contracts however I noticed that due to inheritance, most most modifiers were from inherited contracts and these were not defined in the contracts they were inherited from. I  would suggest declaring modifiers in the contracts in which they are called used.

## Mechanism review
The TapiocaDao is built on the LayerZero Protocol which in itself is a Nascent idea, I would suggest being a little conservative would come in a little handy and as such piece meal deployment would be a good idea since one of the tenets of TapiocaDao is to discourage yield farming locusts.

## Systemic risks
Systemic risk in this project boils down to the attack surface posed by the projects complexity. However I would advise that for audit purposes that can be brought to the barest minimum if comment are used in the codebase to explain various working parts of the project.

### Time spent:
253 hours