---
sidebar_position: 6
---

# Interfaces

Listed below are all of the interfaces used in the aforementioned Furo contracts.

## IBentoBoxMinimal

```solidity
interface IBentoBoxMinimal {
    function balanceOf(address, address) external view returns (uint256);

    function toShare(
        address token,
        uint256 amount,
        bool roundUp
    ) external view returns (uint256 share);

    function toAmount(
        address token,
        uint256 share,
        bool roundUp
    ) external view returns (uint256 amount);

    function registerProtocol() external;

    function deposit(
        address token_,
        address from,
        address to,
        uint256 amount,
        uint256 share
    ) external payable returns (uint256 amountOut, uint256 shareOut);

    function withdraw(
        address token_,
        address from,
        address to,
        uint256 amount,
        uint256 share
    ) external returns (uint256 amountOut, uint256 shareOut);

    function transfer(
        address token,
        address from,
        address to,
        uint256 share
    ) external;

    function setMasterContractApproval(
        address user,
        address masterContract,
        bool approved,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external;
}
```

Interface for a minimal version of the BentoBox contract; source code can be found [here](https://github.com/sushiswap/furo/blob/master/contracts/interfaces/IBentoBoxMinimal.sol).

## IERC20

```solidity
interface IERC20 {
    function totalSupply() external view returns (uint256);

    function balanceOf(address account) external view returns (uint256);

    function allowance(address owner, address spender)
        external
        view
        returns (uint256);

    function approve(address spender, uint256 amount) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(
        address indexed owner,
        address indexed spender,
        uint256 value
    );

    /// @notice EIP 2612
    function permit(
        address owner,
        address spender,
        uint256 value,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external;
}
```

Standard ERC20 interface; source code can be found [here](https://github.com/sushiswap/furo/blob/master/contracts/interfaces/IERC20.sol).

## IFuroStream

```solidity
interface IFuroStream {
    function setBentoBoxApproval(
        address user,
        bool approved,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external;

    function createStream(
        address recipient,
        address token,
        uint64 startTime,
        uint64 endTime,
        uint256 amount, /// @dev in token amount and not in shares
        bool fromBento
    ) external payable returns (uint256 streamId, uint256 depositedShares);

    function withdrawFromStream(
        uint256 streamId,
        uint256 sharesToWithdraw,
        address withdrawTo,
        bool toBentoBox,
        bytes memory taskData
    ) external returns (uint256 recipientBalance, address to);

    function cancelStream(uint256 streamId, bool toBentoBox)
        external
        returns (uint256 senderBalance, uint256 recipientBalance);

    function updateSender(uint256 streamId, address sender) external;

    function updateStream(
        uint256 streamId,
        uint128 topUpAmount,
        uint64 extendTime,
        bool fromBentoBox
    ) external returns (uint256 depositedShares);

    function streamBalanceOf(uint256 streamId)
        external
        view
        returns (uint256 senderBalance, uint256 recipientBalance);

    function getStream(uint256 streamId) external view returns (Stream memory);

    event CreateStream(
        uint256 indexed streamId,
        address indexed sender,
        address indexed recipient,
        address token,
        uint256 amount,
        uint256 startTime,
        uint256 endTime,
        bool fromBentoBox
    );

    event UpdateStream(
        uint256 indexed streamId,
        uint128 indexed topUpAmount,
        uint64 indexed extendTime,
        bool fromBentoBox
    );

    event Withdraw(
        uint256 indexed streamId,
        uint256 indexed sharesToWithdraw,
        address indexed withdrawTo,
        address token,
        bool toBentoBox
    );

    event CancelStream(
        uint256 indexed streamId,
        uint256 indexed senderBalance,
        uint256 indexed recipientBalance,
        address token,
        bool toBentoBox
    );

    struct Stream {
        address sender;
        address token;
        uint128 depositedShares;
        uint128 withdrawnShares;
        uint64 startTime;
        uint64 endTime;
    }
}
```

Interface for the FuroStream contract; source code can be found [here](https://github.com/sushiswap/furo/blob/master/contracts/interfaces/IFuroStream.sol).

## IFuroVesting

```solidity
interface IFuroVesting {
    function setBentoBoxApproval(
        address user,
        bool approved,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external;

    function createVesting(VestParams calldata vestParams)
        external
        payable
        returns (
            uint256 depositedShares,
            uint256 vestId,
            uint128 stepShares,
            uint128 cliffShares
        );

    function withdraw(
        uint256 vestId,
        bytes memory taskData,
        bool toBentoBox
    ) external;

    function stopVesting(uint256 vestId, bool toBentoBox) external;

    function vestBalance(uint256 vestId) external view returns (uint256);

    function updateOwner(uint256 vestId, address newOwner) external;

    struct VestParams {
        IERC20 token;
        address recipient;
        uint32 start;
        uint32 cliffDuration;
        uint32 stepDuration;
        uint32 steps;
        uint128 stepPercentage;
        uint128 amount;
        bool fromBentoBox;
    }

    struct Vest {
        address owner;
        IERC20 token;
        uint32 start;
        uint32 cliffDuration;
        uint32 stepDuration;
        uint32 steps;
        uint128 cliffAmount;
        uint128 stepAmount;
        uint128 claimed;
    }

    event CreateVesting(
        uint256 indexed vestId,
        IERC20 token,
        address indexed owner,
        address indexed recipient,
        uint32 start,
        uint32 cliffDuration,
        uint32 stepDuration,
        uint32 steps,
        uint128 cliffAmount,
        uint128 stepAmount,
        bool fromBentoBox
    );

    event Withdraw(
        uint256 indexed vestId,
        IERC20 indexed token,
        uint256 indexed amount,
        bool toBentoBox
    );

    event CancelVesting(
        uint256 indexed vestId,
        uint256 indexed ownerAmount,
        uint256 indexed recipientAmount,
        IERC20 token,
        bool toBentoBox
    );

    event LogUpdateOwner(uint256 indexed vestId, address indexed newOwner);
}
```

Interface for the FuroVesting contract; source code can be found [here](https://github.com/sushiswap/furo/blob/master/contracts/interfaces/IFuroVesting.sol).

## IPool

```solidity
interface IPool {
    function swap(bytes calldata data)
        external
        returns (uint256 finalAmountOut);

    function flashSwap(bytes calldata data)
        external
        returns (uint256 finalAmountOut);

    function mint(bytes calldata data) external returns (uint256 liquidity);

    function burn(bytes calldata data)
        external
        returns (TokenAmount[] memory withdrawnAmounts);

    function burnSingle(bytes calldata data)
        external
        returns (uint256 amountOut);

    function poolIdentifier() external pure returns (bytes32);

    function getAssets() external view returns (address[] memory);

    function getAmountOut(bytes calldata data)
        external
        view
        returns (uint256 finalAmountOut);

    function getAmountIn(bytes calldata data)
        external
        view
        returns (uint256 finalAmountIn);

    event Swap(
        address indexed recipient,
        address indexed tokenIn,
        address indexed tokenOut,
        uint256 amountIn,
        uint256 amountOut
    );

    struct TokenAmount {
        address token;
        uint256 amount;
    }
}
```

Interface for a Trident pool; source code can be found [here](https://github.com/sushiswap/furo/blob/master/contracts/interfaces/IPool.sol).

## ITasker

```solidity
interface ITasker {
    function onTaskReceived(
        bytes calldata data
    ) external;
}
```

Interface for the Tasker contract; source code can be found [here](https://github.com/sushiswap/furo/blob/master/contracts/interfaces/ITasker.sol).

## ITokenURIFetcher

```solidity
interface ITokenURIFetcher {
    function fetchTokenURIData(uint256 id)
        external
        view
        returns (string memory);
}
```

Interface for the TokenURIFetcher contract; source code can be found [here](https://github.com/sushiswap/furo/blob/master/contracts/interfaces/ITokenURIFetcher.sol).

## ITridentRouter

```solidity
interface ITridentRouter {
    struct Path {
        address pool;
        bytes data;
    }

    struct ExactInputSingleParams {
        uint256 amountIn;
        uint256 amountOutMinimum;
        address pool;
        address tokenIn;
        bytes data;
    }

    struct ExactInputParams {
        address tokenIn;
        uint256 amountIn;
        uint256 amountOutMinimum;
        Path[] path;
    }

    struct TokenInput {
        address token;
        bool native;
        uint256 amount;
    }

    struct InitialPath {
        address tokenIn;
        address pool;
        bool native;
        uint256 amount;
        bytes data;
    }

    struct PercentagePath {
        address tokenIn;
        address pool;
        uint64 balancePercentage; // Multiplied by 10^6. 100% = 100_000_000
        bytes data;
    }

    struct Output {
        address token;
        address to;
        bool unwrapBento;
        uint256 minAmount;
    }

    struct ComplexPathParams {
        InitialPath[] initialPath;
        PercentagePath[] percentagePath;
        Output[] output;
    }
}
```

Interface for the Trident pool router; source code can be found [here](https://github.com/sushiswap/furo/blob/master/contracts/interfaces/ITridentRouter.sol).
