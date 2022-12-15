Link: https://twitter.com/pashovkrum/status/1592454343403659264?s=20&t=tcLHxL00LTr4EVwzrJmnNg

3 security flaws of smart contracts that inherit OpenZeppelin's `Pausable` functionality.

1/ The `pause()` and `unpause()` methods are actually internal, so if your contract does not expose them externally you have no way of using pausability.

2/ It's a centralisation vulnerability if an owner can pause not only inbound, but outbound functionality. It's a best practice to be able to pause inbound (stake) functionality but not outbound (withdraw) methods

3/ Always check for state-changing methods missing the `whenNotPaused()` modifier, especially all inbound ones - this might be a security hole.