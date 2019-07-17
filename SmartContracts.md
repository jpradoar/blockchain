#Smart Contracts


<pre>

# Cuando realizo un minado recibo el siguiente output.
curl -X GET http://192.168.2.2:5000/mine
{
  "index": 4, 
  "message": "New Block Forged", 
  "previous_hash": "b341fae8564007f786a5a5968f332945abce4e6a886aa9a4148670719f284808", 
  "proof": 69554, 
  "transactions": [
    {
      "amount": 1, 
      "recipient": "eeb5dd65350642cf8439901932b41ecd", 
      "sender": "0"
    }
  ]
}


# Cuando quiero realizar una nueva transacción debo colocar el recipient donde supuestamente quier odejar el contenido.
curl -X POST -H "Content-Type: application/json" -d '{"sender": "eeb5dd65350642cf8439901932b41ecd","recipient": "someone-other-address","amount": 5}' "http://192.168.2.2:5000/transactions/new"
{
  "message": "Transaction will be added to Block 5"
}




# Register a new node.
curl -X POST -H "Content-Type: application/json" -d '{"nodes": ["http://192.168.2.2:5001/"] }' http://192.168.2.2:5000/nodes/register
{
  "message": "New nodes have been added", 
  "total_nodes": [
    "192.168.2.2:5001"
  ]
}



#--------------------
#  Info
#--------------------

- Each Block has an index, a timestamp (in Unix time), a list of transactions, a proof (more on that later), and the hash of the previous Block.
- A Proof of Work algorithm (PoW) is how new Blocks are created or mined on the blockchain. The goal of PoW is to discover a number which solves a problem

- Endpoints
	POST => /transactions/new     to create a new transaction to a block  
	GET  => /mine                 to tell our server to mine a new block.
	GET  => /chain                to return the full Blockchain.


#--------------------
#  Docker Infra
#--------------------

# Dockerfile
FROM python:3.6
COPY ./blockchain.py /blockchain.py
RUN chmod +x /blockchain.py; \
apt-get update; \
apt-get install python-pip curl -y; \
pip install Flask==0.12.2 requests==2.18.4; 
CMD ["python", "/blockchain.py"]

# Create blockchain image
docker build -t blockchain_img .

# Run 5 blockchain containers 
for i in 0 1 2 3 4; do docker run -itd -p 500$i:5000 --name Blockchain0$i  blockchain_img:latest;  done

# Register nodes
docker exec Blockchain00 curl -s -X POST -H "Content-Type: application/json" -d '{"nodes": ["http://localhost:5000/"] }' http://localhost:5000/nodes/register


#--------------------
#  Examples
#--------------------

# Register a new node.
curl -X POST -H "Content-Type: application/json" -d '{"nodes": ["http://localhost:5001/"] }' http://localhost:5000/nodes/register

# Get nodes
curl -X GET http://localhost:5000/nodes/resolve

# Mine
curl -X GET http://localhost:5000/mine

# Cuando quiero realizar una nueva transacción debo colocar el recipient donde supuestamente quiero dejar el contenido.
curl -X POST -H "Content-Type: application/json" -d '{
 "sender": "d5502ef5dcf84d0f9d846ff35b5e4037",
 "recipient": "el-recipient-de-pepito",
 "amount": 150
}' "http://localhost:5000/transactions/new"


# Chain
curl -X GET http://localhost:5000/chain



########################################################################

# smart_contract

pragma solidity ^0.4.0;  

contract SimpleStorage {
    uint storedData;

    function set(uint x) {
        storedData = x;
    }

    function get() constant returns (uint) {
        return storedData;
    }
}







############### multichaind server01 ######################

apt-get install sqlite3 libsqlite3-dev && apt-get install python-dev && apt-get install python-pip && pip install --upgrade pip && pip install pycrypto

cd /tmp;  wget https://www.multichain.com/download/multichain-1.0.5.tar.gz; tar -xvzf multichain-1.0.5.tar.gz; cd multichain-1.0.5; mv multichaind multichain-cli multichain-util /usr/local/bin


git clone https://github.com/MultiChain/multichain-explorer.git && cd multichain-explorer && python setup.py install --user && sudo python setup.py install

multichain-util create chain1 && multichaind chain1 -daemon

	MultiChain 1.0.5 Daemon (latest protocol 10011)
	Starting up node...
	Looking for genesis block...
	Genesis block found

	Other nodes can connect to this node using:
	multichaind chain1@192.168.1.50:8343

	This host has multiple IP addresses, so from some networks:
	multichaind chain1@172.17.0.1:8343

	Listening for API requests on port 8342 (local only - see rpcallowip setting)
	Node ready.


cd ~/.multichain/chain1/
cat multichain.conf















ubuntu@ubuntu:~/multichain-explorer$ multichaind chain1 -daemon

MultiChain 1.0.5 Daemon (latest protocol 10011)

Starting up node...

Looking for genesis block...
Genesis block found

Other nodes can connect to this node using:
multichaind chain1@192.168.1.50:4381

This host has multiple IP addresses, so from some networks:
multichaind chain1@172.17.0.1:4381

Listening for API requests on port 4380 (local only - see rpcallowip setting)

Node ready.


############### multichaind node01 ######################

 multichaind chain1@192.168.1.50:6835

MultiChain 1.0.5 Daemon (latest protocol 10011)

Retrieving blockchain parameters from the seed node 192.168.1.50:6835 ...
Blockchain successfully initialized.

Please ask blockchain admin or user having activate permission to let you connect and/or transact:
multichain-cli chain1 grant 1GaZbNxxxxxxxxxxxxxxxxxxxxrDM16sxH1 connect
multichain-cli chain1 grant 1GaZbNxxxxxxxxxxxxxxxxxxxxrDM16sxH1 connect,send,receive



</pre>


