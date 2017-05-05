- Make sure your chaincode is free from syntax errors

Command:
go build




- Push your chaincode in the public GitHub (remote repo) 

Example:
https://github.com/pong-pantola/blockchain-sample


Note:
-->the public GitHub is not the IBM GitHub
-->If you made any changes to your local repo, do not forget to push your updates to your GithHub (remote repo)
-->The deploy command you will use later will be looking at your remote repo and not at your local repo


- In Bluemix, go to the Blockchain Dashboard





- Under "Network", copy the URL of Validating Peer 0.

Example: https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003

Note: Use your VP0's URL to customize the sample commands below.



- Under "APIs", expand "Network's Enroll IDs"





- Copy the Secret password of user_type1_0 to user_type1_3.

Example:
user_type1_0 f451f01dec
user_type1_1 df899e36d8
user_type1_2 587b1957c6
user_type1_3 c113e2e529

Note: Use your users' set of passwords to customize the commands below.



- Login using user_type1_3 (when using the curl command, you may use your docker terminal)

Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/registrar -d '{ "enrollId": "user_type1_3", "enrollSecret": "c113e2e529" }'

Output:
{"OK":"Login successful for user 'user_type1_3'."}


Note: 
Do not attempt to login with an incorrect password.  I noticed that when I use a wrong password in my first attempt, and use the correct password in my second attempt, I cannot anymore login successfully.
If you accidentally use a wrong password, just use a different user from the list



- Deploy your chaincode using user_type1_3

Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "deploy", "params": { "type":1, "chaincodeID":{"path":"https://github.com/pong-pantola/blockchain-sample" }, "ctorMsg": { "function":"createTable", "args":[] }, "secureContext": "user_type1_3"}, "id": 1 }'


https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003