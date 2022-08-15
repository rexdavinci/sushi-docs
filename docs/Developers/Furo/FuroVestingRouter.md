---
sidebar_position: 5
---

# FuroVestingRouter

FuroVestingRouter is the contract that is actually utilized directly for vesting tokens.

The full contract can be found [here](https://github.com/sushiswap/sushiswap/blob/master/protocols/furo/contracts/FuroVestingRouter.sol).

## Functions

### setBentoBoxApproval

```solidity
function setBentoBoxApproval(
    address user,
    bool approved,
    uint8 v,
    bytes32 r,
    bytes32 s
  ) external payable
```

Approves this contract for BentoBox.

#### Parameters

| Name       | Type    | Description                              |
| :--------- | :------ | :--------------------------------------- |
| `user`     | address | user address to set as owner in BentoBox |
| `approved` | bool    | boolean if approved or not by BentoBox   |
| `v`        | uint8   | recovery byte of the signature           |
| `r`        | bytes32 | half of the ECDSA signature pair         |
| `s`        | bytes32 | half of the ECDSA signature pair         |

### createVesting

```solidity
function createVesting(IFuroVesting.VestParams memory vestParams, uint256 minShare)
    external
    payable
    returns (
      uint256 depositedShares,
      uint256 vestId,
      uint128 stepShares,
      uint128 cliffShares
    )
```

Creates a vesting.

#### Parameters

| Name         | Type                    | Description                                                        |
| :----------- | :---------------------- | :----------------------------------------------------------------- |
| `vestParams` | IFuroVesting.VestParams | info necessary to create a new vesting (start, cliff, steps, etc.) |
| `minShare`   | uint256                 | minimum amount of shares                                           |

#### Returns

| Name              | Type    | Description                |
| :---------------- | :------ | :------------------------- |
| `depositedShares` | uint256 | amount of shares deposited |
| `vestId`          | uint256 | new vesting ID             |
| `stepShares`      | uint128 | amount of step shares      |
| `cliffShares`     | uint128 | amount of cliff shares     |

### \_depositToken

```solidity
function _depositToken(
    address token,
    address from,
    address to,
    uint256 amount,
    bool fromBentoBox
  ) internal returns (uint256 depositedShares)
```

Internal function that deposits a token into a vesting.

#### Parameters

| Name           | Type    | Description                                              |
| :------------- | :------ | :------------------------------------------------------- |
| `token`        | address | address of token to deposit                              |
| `from`         | address | address of user depositing tokens                        |
| `to`           | address | address of token recipient                               |
| `amount`       | uint256 | amount of tokens to deposit                              |
| `fromBentoBox` | bool    | boolean of whether update is coming from BentoBox or not |

#### Returns

| Name              | Type    | Description                           |
| :---------------- | :------ | :------------------------------------ |
| `depositedShares` | uint256 | amount of shares deposited in vesting |