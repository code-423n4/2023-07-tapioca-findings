# Missing additional safety for execute may lead to lost ETH in future

Context-1: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/bigBang/BigBang.sol#L209-L223
Context-2: https://github.com/Tapioca-DAO/tapioca-bar-audit/blob/master/contracts/markets/singularity/Singularity.sol#L181-L195

## Description

If the function execute() would be payable, then multiple delegated-to functions could use the
same msg.value , which could result in losing ETH from the pair. A future upgrade of Solidity might change the
default setting for function to payable. See Solidity issue#12539 (https://github.com/ethereum/solidity/issues/12539).

    function execute(
        bytes[] calldata calls,
        bool revertOnFail
    ) external returns (bool[] memory successes, string[] memory results) {
        successes = new bool[](calls.length);
        results = new string[](calls.length);
        for (uint256 i = 0; i < calls.length; i++) {
            (bool success, bytes memory result) = address(this).delegatecall(
                calls[i]
            );
			....
			....
        }
    }
			
## Recommendation

Consider adding a check for msg.value to be future proof and extra safe. Alternatively make
a comment about the risk.

    function execute(
        bytes[] calldata calls,
        bool revertOnFail
    ) external returns (bool[] memory successes, string[] memory results) {
		require(msg.value == 0);
        successes = new bool[](calls.length);
        results = new string[](calls.length);
        for (uint256 i = 0; i < calls.length; i++) {
            (bool success, bytes memory result) = address(this).delegatecall(
                calls[i]
            );
			....
			...
        }
    }


# Function rebalance(....) allows 0 amount of native assets

Context: https://github.com/Tapioca-DAO/tapiocaz-audit/blob/master/contracts/Balancer.sol#L202-L208

## Description

The function rebalance(...) disallows amount == 0 for ERC20, however it does allow amount == 0 for native assets.

    function rebalance(
        address payable _srcOft,
        uint16 _dstChainId,
        uint256 _slippage,
        uint256 _amount,
        bytes memory _ercData
    )
        external
        payable
        onlyOwner
        onlyValidDestination(_srcOft, _dstChainId)
        onlyValidSlippage(_slippage)
    {
        ......

        //send
        bool _isNative = ITapiocaOFT(_srcOft).erc20() == address(0);
        if (_isNative) {
            if (msg.value <= _amount) revert FeeAmountNotSet();
            _sendNative(_srcOft, _amount, _dstChainId, _slippage);
        } else {
            if (msg.value == 0) revert FeeAmountNotSet();
            _sendToken(_srcOft, _amount, _dstChainId, _slippage, _ercData);
        }

        .....
    }



## Recommendation

Consider also checking amount == 0 for native assets.

# Use block.timestamp at many places in the codebase
context-1: https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L117
context-2: https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L161
context-3: https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L170
context-4: https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L171
context-5: https://github.com/Tapioca-DAO/tap-token-audit/blob/main/contracts/Vesting.sol#L172

## Description

There are many instances of block.timestamp usage within the project. block.timestamp can be influenced by miners to a certain degree, so developers should be aware that this may have some risk if miners collude on time manipulation to influence the price oracles.

## Recommendation

Use block.number instead of block.timestamp or now to reduce the risk of Maximal Extractable Value (MEV) attacks. Check if the timescale of the project occurs across years, days and months rather than seconds. If possible, it is recommended to use Oracles.

