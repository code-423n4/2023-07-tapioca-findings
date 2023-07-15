Inside governance `twTap.sol`  pool.totalDeposited is used 3 times.

276: `` computeMinWeight(pool.totalDeposited, MIN_WEIGHT_FACTOR);``
298: ``pool.totalDeposited += _amount;``
554: ``pool.totalDeposited -= position.tapAmount;``

my suggestion is that if it is extracted into a variable, then it can be reused as many times needed and it will save some amount of gas
