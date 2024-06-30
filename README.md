# Starter Next/Hardhat Project

After cloning the github, you will want to do the following to get the code running on your computer.

1. Inside the project directory, in the terminal type: npm i
2. Open two additional terminals in your VS code
3. In the second terminal type: npx hardhat node
4. In the third terminal, type: npx hardhat run --network localhost scripts/deploy.js
5. Back in the first terminal, type npm run dev to launch the front-end.

After this, the project will be running on your localhost. 
Typically at http://localhost:3000/

# Assessment Smart Contract

## Overview

The `Assessment` smart contract is a simple Solidity-based contract that allows the owner to manage funds by depositing and withdrawing them. This contract ensures that only the owner can perform these actions and provides transparency through events and custom errors.

## Features

- **Owner Management**: Only the owner of the contract can deposit and withdraw funds.
- **Balance Tracking**: The contract maintains a balance state variable to keep track of the funds.
- **Events**: Emit events for deposit and withdraw actions to provide a clear log of transactions.
- **Custom Errors**: Implement custom errors for better error handling and debugging.

## Contract Details

### Variables

- `address payable public owner`: The owner of the contract, set during contract deployment.
- `uint256 public balance`: The current balance of the contract.

### Events

- `event Deposit(uint256 amount)`: Emitted when a deposit is made.
- `event Withdraw(uint256 amount)`: Emitted when a withdrawal is made.

### Constructor

The constructor sets the initial owner and balance of the contract.

```solidity
constructor(uint initBalance) payable {
    owner = payable(msg.sender);
    balance = initBalance;
}
```

### Functions

#### `getBalance()`

Returns the current balance of the contract.

```solidity
function getBalance() public view returns(uint256) {
    return balance;
}
```

#### `deposit(uint256 _amount)`

Allows the owner to deposit a specified amount into the contract.

- Requires that the caller is the owner.
- Increases the balance by the specified amount.
- Emits a `Deposit` event.

```solidity
function deposit(uint256 _amount) public payable {
    uint _previousBalance = balance;

    // make sure this is the owner
    require(msg.sender == owner, "You are not the owner of this account");

    // perform transaction
    balance += _amount;

    // assert transaction completed successfully
    assert(balance == _previousBalance + _amount);

    // emit the event
    emit Deposit(_amount);
}
```

#### `withdraw(uint256 _withdrawAmount)`

Allows the owner to withdraw a specified amount from the contract.

- Requires that the caller is the owner.
- Checks if the balance is sufficient for the withdrawal.
- Decreases the balance by the specified amount.
- Emits a `Withdraw` event.
- Throws a custom error `InsufficientBalance` if the balance is insufficient.

```solidity
function withdraw(uint256 _withdrawAmount) public {
    require(msg.sender == owner, "You are not the owner of this account");
    uint _previousBalance = balance;
    if (balance < _withdrawAmount) {
        revert InsufficientBalance({
            balance: balance,
            withdrawAmount: _withdrawAmount
        });
    }

    // withdraw the given amount
    balance -= _withdrawAmount;

    // assert the balance is correct
    assert(balance == (_previousBalance - _withdrawAmount));

    // emit the event
    emit Withdraw(_withdrawAmount);
}
```

### Custom Error

- `error InsufficientBalance(uint256 balance, uint256 withdrawAmount)`: Custom error for handling insufficient balance during withdrawals.

## Deployment

To deploy this contract, you need to use a Solidity-compatible environment like Hardhat or Remix. The constructor requires an initial balance to be passed during deployment.

```solidity
constructor(uint initBalance) payable {
    owner = payable(msg.sender);
    balance = initBalance;
}
```

## Usage

1. **Deploy the contract**: Deploy the contract with an initial balance.
2. **Deposit funds**: Call the `deposit` function to add funds to the contract (only by the owner).
3. **Withdraw funds**: Call the `withdraw` function to remove funds from the contract (only by the owner).
4. **Check balance**: Call the `getBalance` function to view the current balance.

## Conclusion

The `Assessment` smart contract provides a simple yet effective way to manage funds securely on the Ethereum blockchain, ensuring only the owner has control over deposits and withdrawals.
