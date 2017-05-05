Make sure your chaincode is free from syntax errors
===================================================
Command:
go build

Note: You may just fork the GitHub repository below if you simply want to try this tutorial




Push your chaincode in the public GitHub (remote repo) 
======================================================
Example:
https://github.com/pong-pantola/blockchain-sample


Note:
-->the public GitHub is not the IBM GitHub
-->VERY IMPORTANT: If you made any changes to your local repo, do not forget to push your updates to your GithHub (remote repo)
-->The deploy command you will use later will be looking at your remote repo and not at your local repo





In Bluemix, create a Blockchain Service go to the Blockchain Dashboard
==========================================
-->Login to your Bluemix account
-->Click "Catalog"
-->Click "Blockchain"
-->Make sure that the selected plan is "Starter Developer plan (beta)"
-->Click "Launch Dashboard"



In the dashboard, under "Network", copy the URL of Validating Peer 0
====================================================================
Example: https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003

Note: Use your VP0's URL to customize the sample commands below.  Do not use the sample URL above.




Under "APIs", expand "Network's Enroll IDs" and Copy the Secret password of user_type1_0 to user_type1_3
========================================================================================================
Example:
user_type1_0 f451f01dec
user_type1_1 df899e36d8
user_type1_2 587b1957c6
user_type1_3 c113e2e529

Note: Use your users' passwords to customize the commands below.  Do not use the sample passwords above.





Login using user_type1_3 (when using the curl command, you may use your docker terminal)
========================================================================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/registrar -d '{ "enrollId": "user_type1_3", "enrollSecret": "c113e2e529" }'

Output:
{"OK":"Login successful for user 'user_type1_3'."}


Note: 
Do not attempt to login with an incorrect password.  I noticed that when I use a wrong password in my first attempt, and use the correct password in my second attempt, I cannot anymore login successfully.
If you accidentally use a wrong password, just use a different user from the list




Deploy your chaincode using user_type1_3
========================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "deploy", "params": { "type":1, "chaincodeID":{"path":"https://github.com/pong-pantola/blockchain-sample" }, "ctorMsg": { "function":"createTable", "args":[] }, "secureContext": "user_type1_3"}, "id": 1 }'

Output:
{"jsonrpc":"2.0","result":{"status":"OK","message":"26407760ffc3f26faaa019d5d8e21dff7d9d95a809f68a40c4492cda000676d2bca8aed86f73e960fa557fd05f9ef66c9693806925e20212e82ca81e6fe3c6d4"},"id":1}

Note:
-->Deployment may take a few seconds
-->Do not forget to update the chaincodeID to point to your public GitHub (remote repo) when you issue the command above
-->Take note of the output, specifically the long hash value which will serve as the chaincodeID for the invoke and query later
-->If you made some changes to your chaincode, make sure to repeat the process (e.g., push your changes to your remote repo, redeploy, etc.)




Insert an entry (e.g., Alice with an age of 25) in the ledger
=============================================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "invoke", "params": { "type":1, "chaincodeID":{ "name":"26407760ffc3f26faaa019d5d8e21dff7d9d95a809f68a40c4492cda000676d2bca8aed86f73e960fa557fd05f9ef66c9693806925e20212e82ca81e6fe3c6d4" }, "ctorMsg": { "function":"insertBlockchainSample","args":["alice", "25"] }, "secureContext": "user_type1_3" }, "id": 3 }'

Output:
{"jsonrpc":"2.0","result":{"status":"OK","message":"<SOME NUMBER>"},"id":3}

Note:
Make sure that you replaced the chaincodeID with the one indicated in the output of Deploy (refer to previous step)




Query the inserted entry (e.g., Alice) in the ledger
====================================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "query", "params": { "type":1, "chaincodeID":{ "name":"26407760ffc3f26faaa019d5d8e21dff7d9d95a809f68a40c4492cda000676d2bca8aed86f73e960fa557fd05f9ef66c9693806925e20212e82ca81e6fe3c6d4" }, "ctorMsg": {"function":"getBlockchainSample", "args":["alice"] }, "secureContext": "user_type1_3" }, "id": 5 }'

Output:
{"jsonrpc":"2.0","result":{"status":"OK","message":"{\"Stub\":{\"TxID\":\"<SOME NUMBER\"},\"TableName\":\"BlockchainSample\",\"Name\":\"alice\",\"Age\":25,\"ErrMsg\":\"\"}"},"id":5}




Insert another entry (e.g., Bob with an age of 20) in the ledger
================================================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "invoke", "params": { "type":1, "chaincodeID":{ "name":"26407760ffc3f26faaa019d5d8e21dff7d9d95a809f68a40c4492cda000676d2bca8aed86f73e960fa557fd05f9ef66c9693806925e20212e82ca81e6fe3c6d4" }, "ctorMsg": { "function":"insertBlockchainSample","args":["bob", "20"] }, "secureContext": "user_type1_3" }, "id": 3 }'

Output:
{"jsonrpc":"2.0","result":{"status":"OK","message":"<SOME NUMBER>"},"id":3}




Query the inserted entry (e.g., Bob) in the ledger
==================================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "query", "params": { "type":1, "chaincodeID":{ "name":"26407760ffc3f26faaa019d5d8e21dff7d9d95a809f68a40c4492cda000676d2bca8aed86f73e960fa557fd05f9ef66c9693806925e20212e82ca81e6fe3c6d4" }, "ctorMsg": {"function":"getBlockchainSample", "args":["bob"] }, "secureContext": "user_type1_3" }, "id": 5 }'

Output:
{"jsonrpc":"2.0","result":{"status":"OK","message":"{\"Stub\":{\"TxID\":\"<SOME NUMBER\"},\"TableName\":\"BlockchainSample\",\"Name\":\"bob\",\"Age\":20,\"ErrMsg\":\"\"}"},"id":5}





Increase the age of an entry (e.g., Bob's age will be increased by 5 to make it 25) in the ledger
=================================================================================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "invoke", "params": { "type":1, "chaincodeID":{ "name":"26407760ffc3f26faaa019d5d8e21dff7d9d95a809f68a40c4492cda000676d2bca8aed86f73e960fa557fd05f9ef66c9693806925e20212e82ca81e6fe3c6d4" }, "ctorMsg": { "function":"increaseAge","args":["bob", "5"] }, "secureContext": "user_type1_3" }, "id": 3 }'

Output:
{"jsonrpc":"2.0","result":{"status":"OK","message":"<SOME NUMBER>"},"id":3}




Query the entry with updated age (e.g., Bob) in the ledger to see if the age has increased
==========================================================================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "query", "params": { "type":1, "chaincodeID":{ "name":"26407760ffc3f26faaa019d5d8e21dff7d9d95a809f68a40c4492cda000676d2bca8aed86f73e960fa557fd05f9ef66c9693806925e20212e82ca81e6fe3c6d4" }, "ctorMsg": {"function":"getBlockchainSample", "args":["bob"] }, "secureContext": "user_type1_3" }, "id": 5 }'

Output:
{"jsonrpc":"2.0","result":{"status":"OK","message":"{\"Stub\":{\"TxID\":\"<SOME NUMBER>\"},\"TableName\":\"BlockchainSample\",\"Name\":\"bob\",\"Age\":25,\"ErrMsg\":\"\"}"},"id":5}




Query all entries with a particulare age (e.g., 25) in the ledger
=================================================================
Command:
curl -i -X POST -H "Content-Type:application/json" https://bfe62a9d9e684939b84a1b23bc7a4b87-vp0.us.blockchain.ibm.com:5003/chaincode -d '{ "jsonrpc": "2.0", "method": "query", "params": { "type":1, "chaincodeID":{ "name":"26407760ffc3f26faaa019d5d8e21dff7d9d95a809f68a40c4492cda000676d2bca8aed86f73e960fa557fd05f9ef66c9693806925e20212e82ca81e6fe3c6d4" }, "ctorMsg": {"function":"getAllBlockchainSampleByAge", "args":["25"] }, "secureContext": "user_type1_3" }, "id": 5 }'

Output:
<STILL ENCOUNTERING A PROBLEM.  STILL TROUBLESHOOTING>