1. Follow Checks-Effects-Interactions
- Checks: Input validations and other validations
- Effects: Update the final storage
- Interaction: Then you may interact with other contract or EOA

2. Loop iterations can grow beyond block gasLimit: Always a possible vulnerability if no of loops depends upon storage variable
- Also a bug if it's present in view function and this function is used by some other function as part of on-chain operations

3. Send and transfer passes `Gas stipend`: 2300 gas which is not enough to make state changes.
- Call passes all remaining gas: But it returns error code instead of reverting(Send also returns bool). So, always check for return values in case of call
- If send or transfer is used, the receiving contract can block progress i.e. they use all gas and run out of gas
- It can happen in call too, receiving contract can assert(), consuming all remaining gas
- Best approach is use withdraw pattern instead of send pattern

4. Overflow underflow: Even if there's by default check in solidity about it, there can be bug.
- It always revert if there's overflow or underflow. So, if it's not possible to avoid them, then contract is stuck in certain state.
- Recommendation: Try to validate input. Try to use require to limit the size of inputs to a reasonable range

5. Clearing mapping: if a mapping is a base type of array or a member of struct array, then deleting or popping array will have no effect on mapping. It's information stays alive in contract. To truly delete a mapping we have to know it's key.Solidity itself doesn't keep track of keys.
- Recommendation: If it truly need to be deleted then consider using library [Iterable mapping](https://github.com/ethereum/dapp-bin/blob/master/library/iterable_mapping.sol)

6. msg.data in argument types less than bytes32: Types that do not occupy the full 32 bytes might contain “dirty higher order bits”.
- function foo(uint8 x)- calling it with 0xff000001 and 0x00000001, both will have argument 1 as far as x is concerned. But msg.data for both will be different. So keccak256(msg.data) will produce different results.

7. Call to unknown contract might lead to DOS: [5th point](https://medium.com/loom-network/how-to-secure-your-smart-contracts-6-solidity-vulnerabilities-and-how-to-avoid-them-part-2-730db0aa4834)
- always assume that transfers and external calls (of instance) can trigger revert

8. Self destruct doesn't trigger fallback

9. Sender and recipient can be same in transfer function. Look if there's any consequences of it.
- [Immunefi Challenge](https://twitter.com/immunefi/status/1602785563836547073?s=20&t=Wma_twA5EfHcHo5cmM7s2w)

10. Always check or validate input

11. when byte32 is converted to byte16 its taken from the left (0x922aba368bee9844aefc4b47b1d58d2857781b382dc1ad896d512e19131d108f -> 0x922aba368bee9844aefc4b47b1d58d28) https://gist.github.com/kenjirai/d1b9c135117eb39d7891d658cbd6154c
- when uint32 is converted to uint16 then the smallest bytes are taken 0x12345678 -> 0x5678

12. If you declare a new storage struct in your function, it will overwrite other globally stored variables (starting from the first slot)