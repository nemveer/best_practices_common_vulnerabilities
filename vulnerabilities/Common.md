1. Some transfer method of ERC20 return bool. So if we call transfer method from our contract we should check for true.
   NO, coz some ERC20 don't return anything. So if we had checked for bool, our txn reverts in this case. So always use Openzeppelin's safeERC20.
   It uses call to call the transfer function and check for bool if returndata.length > 0
   Link: https://twitter.com/0xadrii/status/1588845489092718594?s=20&t=tcLHxL00LTr4EVwzrJmnNg

2. Approval flaw: Openzeppelin's normal erc20's approval method just assign the value in mapping. So if some one wants to reduce their allowance to someone,
   Their new tx can be front-run by spender
   Link: https://twitter.com/0xadrii/status/1596839911998382080?s=20&t=tcLHxL00LTr4EVwzrJmnNg

3. (bool success,) = payable(receiver).call{gas: 3000, value: amount}(hex"");
- Now (bool success, ) is actually the same as writing (bool success, bytes memory data) which basically means that even though the data is omitted it doesn’t mean that the contract does not handle it.
  Actually, the way it works is the bytes data that was returned from the receiver will be copied to memory. Memory allocation becomes very costly if the payload is big
  Link: M03- https://github.com/pashov/audits/blob/master/solo/Zerem-security-review.md


  