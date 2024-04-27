
### Safe Randomness in Smart Contracts: A Tutorial
The importance of secure random number generation within smart contracts is often overlooked, but it plays a significant role. This tutorial aims to clarify this concept by demonstrating why using common methods to generate random numbers can be unsafe and introducing an alternative solution using oracles.

Unsafe Pattern
Imagine a simple Casino contract that has a `coin_toss` function (irrelevant details):
```rust
fn coin_toss(&mut self) -> bool {
    // Logic to implement randomness
}
```
The `play` function allows users to play the game:
```ink
#[ink(message)]
pub fn play(&mut self) {
    // User pays a fee to the Casino

    let toss = self.coin_toss();

    if toss {
        // Player wins - they receive some token
    } else {
        // Player loses
    }
}
```
The Problem with `coin_toss`
While the user pays a fee to play, there is an unsafe pattern that can be exploited. A malicious user could create a contract that checks the outcome of the game without actually playing. If they lose, the transaction is reverted and only gas fees are incurred. If they win, they keep the winnings. This risk-free gambling exploits the predictable nature of `coin_toss`.

```rust
#[ink(message)]
pub fn play_to_win(&mut self) {
    // Call the `play` message from the Casino contract
    let won = // Code that checks if there was a win, for example checking if the balance of a specific token increased
    if !won {
        panic!("Hehe");
    }
}
```

### Safe Randomness with Oracles
To ensure fair play in smart contracts, we should rely on randomness oracles. A randomness oracle is an external service that provides unpredictable and verifiable randomness on the blockchain. It can be used to eliminate predictability issues and protect against exploitation. For example, the DIA oracle is a popular choice for secure random number generation.

To better understand this concept, let's look at an example implementation. In this example, a user first registers their bet with the `register_bet` message. This function collects the bet fee from the user and saves the bet data in the contract's storage.

Next, the contract fetches randomness for the current round from the oracle:
```rust
#[ink(message)]
pub fn get_random(&self, key: u64) -> Option<Vec<u8>> {
    self.oracle.get_random_value_for_round(key)
}
```
Based on the retrieved randomness, the game determines if a user wins or loses. Rewards are then distributed and the bet is removed by the `resolve_bet` message after waiting for a few blocks. This approach requires two transactions from the user: registering their bet with `register_bet` and resolving it with `resolve_bet`.

While this method may be less user-friendly, it ensures fairness and prevents exploitation of the system. Always make sure to use trusted and secure randomness oracles in your smart contract applications.
```