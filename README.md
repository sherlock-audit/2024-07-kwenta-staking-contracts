
# Kwenta Staking Contracts contest details

- Join [Sherlock Discord](https://discord.gg/MABEWyASkp)
- Submit findings using the issue page in your private contest repo (label issues as med or high)
- [Read for more details](https://docs.sherlock.xyz/audits/watsons)

# Q&A

### Q: On what chains are the smart contracts going to be deployed?
Contracts are going to be deployed on Optimism.
___

### Q: If you are integrating tokens, are you allowing only whitelisted tokens to work with the codebase or any complying with the standard? Are they assumed to have certain properties, e.g. be non-reentrant? Are there any types of [weird tokens](https://github.com/d-xo/weird-erc20) you want to integrate?
We are allowing only whitelisted tokens to work with the codebase (USDC & KWENTA) that follows the ERC-20 standard.
___

### Q: Are there any limitations on values set by admins (or other roles) in the codebase, including restrictions on array lengths?
No.
___

### Q: Are there any limitations on values set by admins (or other roles) in protocols you integrate with, including restrictions on array lengths?
No.
___

### Q: For permissioned functions, please list all checks and requirements that will be made before calling the function.
## 1. `unstakeEscrowSkipCooldown(address _account, uint256 _amount)`

- **Modifier**: `onlyRewardEscrow`
- **Description**: This modifier restricts the function to be callable only by the `rewardEscrow` contract.
- **Checks**:
  - **Permission Check**: The `onlyRewardEscrow` modifier ensures that only the `rewardEscrow` contract can call this function, providing a security layer to prevent unauthorized access.
  - **Amount Check**: Ensures `_amount` is non-zero and does not exceed the caller's escrowed balance.

## 2. `stakeEscrowOnBehalf(address _account, uint256 _amount)`

- **Modifier**: `onlyOperator(address _account)`
- **Description**: This modifier checks if the caller is an approved operator for the `_account`.
- **Checks**:
  - **Operator Approval Check**: The `onlyOperator` modifier ensures that only approved operators can call this function on behalf of the specified `_account`.
  - **Amount Check**: Ensures `_amount` is non-zero and does not exceed the unstaked escrowed balance of the `_account`.

## 3. `getRewardOnBehalf(address _account)`

- **Modifier**: `onlyOperator(address _account)`
- **Description**: This modifier restricts the function to be called only by an approved operator of the `_account`.
- **Checks**:
  - **Operator Approval Check**: The `onlyOperator` modifier ensures that only approved operators can call this function on behalf of the specified `_account`.

## 4. `compoundOnBehalf(address _account)`

- **Modifier**: `onlyOperator(address _account)`
- **Description**: This modifier checks if the caller is an approved operator for the `_account`.
- **Checks**:
  - **Operator Approval Check**: The `onlyOperator` modifier ensures that only approved operators can call this function on behalf of the specified `_account`.

## 5. `notifyRewardAmount(uint256 _reward, uint256 _rewardUsdc)`

- **Modifier**: `onlyRewardsNotifier`
- **Description**: This modifier restricts the function to be callable only by the `rewardsNotifier` contract.
- **Checks**:
  - **Permission Check**: The `onlyRewardsNotifier` modifier ensures that only the `rewardsNotifier` contract can call this function, providing secure access control.
  - **Calculation Check**: The function calculates the new reward rates based on the current period's remaining time and updates the reward rates and duration accordingly. Ensures the reward amount and duration are correctly handled.

## 6. `setRewardsDuration(uint256 _rewardsDuration)`

- **Modifier**: `onlyOwner`
- **Description**: This modifier restricts the function to be callable only by the contract owner.
- **Checks**:
  - **Owner Check**: The `onlyOwner` modifier ensures that only the contract owner can modify the rewards duration.
  - **Period Check**: Ensures that the new rewards duration can only be set if the current rewards period is complete.
  - **Zero Duration Check**: Ensures that the rewards duration cannot be set to zero.

## 7. `setCooldownPeriod(uint256 _cooldownPeriod)`

- **Modifier**: `onlyOwner`
- **Description**: This modifier restricts the function to be callable only by the contract owner.
- **Checks**:
  - **Owner Check**: The `onlyOwner` modifier ensures that only the contract owner can modify the cooldown period.
  - **Cooldown Period Range Check**: Ensures the new cooldown period is within the acceptable range, between `MIN_COOLDOWN_PERIOD` and `MAX_COOLDOWN_PERIOD`.

## 8. `pauseStakingRewards()`

- **Modifier**: `onlyOwner`
- **Description**: This modifier restricts the function to be callable only by the contract owner.
- **Checks**:
  - **Owner Check**: The `onlyOwner` modifier ensures that only the contract owner can pause the staking rewards.

## 9. `unpauseStakingRewards()`

- **Modifier**: `onlyOwner`
- **Description**: This modifier restricts the function to be callable only by the contract owner.
- **Checks**:
  - **Owner Check**: The `onlyOwner` modifier ensures that only the contract owner can unpause the staking rewards.


These are the checks and requirements when calling the permissioned functions of StakingRewardsV2 contract.

___

### Q: Is the codebase expected to comply with any EIPs? Can there be/are there any deviations from the specification?
The codebase in scope is not expected to comply with any EIPs.
___

### Q: Are there any off-chain mechanisms or off-chain procedures for the protocol (keeper bots, arbitrage bots, etc.)?
No.
___

### Q: Are there any hardcoded values that you intend to change before (some) deployments?
New rewardsNotifier will be set at the upgrade to match the new notifier that will also distribute USDC rewards.
___

### Q: If the codebase is to be deployed on an L2, what should be the behavior of the protocol in case of sequencer issues (if applicable)? Should Sherlock assume that the Sequencer won't misbehave, including going offline?
Sherlock should assume that the Sequencer won't misbehave.
___

### Q: Should potential issues, like broken assumptions about function behavior, be reported if they could pose risks in future integrations, even if they might not be an issue in the context of the scope? If yes, can you elaborate on properties/invariants that should hold?
Any potential misbehaviour regarding staking that is not an issue right now but could pose risk in future integrations should be counted, but reported as Low issues.
___

### Q: Please discuss any design choices you made.
No specific design choices were made for this specific upgrade.
___

### Q: Please list any known issues and explicitly state the acceptable risks for each known issue.
Users trust the contract owner to exercise their upgrade and permission capabilities responsibly and to avoid actions that could harm users or the system. Similarly, users place their trust in the operators they designate to act in their best interests and to adhere to the permissions granted to them.
___

### Q: We will report issues where the core protocol functionality is inaccessible for at least 7 days. Would you like to override this value?
No.
___

### Q: Please provide links to previous audits (if any).
A list of the previous conducted audits can be found here :

https://github.com/Kwenta/token/tree/main/audits/external
___

### Q: Please list any relevant protocol resources.
KIP127 - (https://gov.kwenta.eth.limo/kips/kip-127/)

Removing the staking cooldown and deprecating the threshold will be part of a future release and are not part of this upgrade.

___

### Q: Additional audit information.
If there are no USDC rewards to be distributed then it doesnt count as a denial of service attack.
___



# Audit scope


[token @ 38114f26b5cc19e59ab5ff78578855df7bc41f85](https://github.com/Kwenta/token/tree/38114f26b5cc19e59ab5ff78578855df7bc41f85)
- [token/contracts/StakingRewardsNotifier.sol](token/contracts/StakingRewardsNotifier.sol)
- [token/contracts/StakingRewardsV2.sol](token/contracts/StakingRewardsV2.sol)
- [token/contracts/interfaces/IStakingRewardsNotifier.sol](token/contracts/interfaces/IStakingRewardsNotifier.sol)
- [token/contracts/interfaces/IStakingRewardsV2.sol](token/contracts/interfaces/IStakingRewardsV2.sol)


