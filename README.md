# Multiple MasterNode

A script which is easily create Multiple Masternodes of the same coin in the same VPS.

First of all you have to start one masternode using <a href="https://github.com/cnote-chain/CNOTE-MN/blob/main/CNOTE-MN-v2.sh">CNOTE-MN-v2.sh</a> script that is your MainNode.

# Guide of use CNOTE-MN-v2.sh for MainNode:

```
wget -q https://raw.githubusercontent.com/cnote-chain/CNOTE-MN/main/CNOTE-MN-v2.sh
sudo chmod +x CNOTE-MN-v2.sh
./CNOTE-MN-v2.sh
```
***

# Guide for Install MultiMN:

Install the multimn script 

`curl -sL https://raw.githubusercontent.com/cnote-chain/CNOTE-MultiMN/main/multimn_install.sh | sudo -E bash -`

Add the coin profile.
```
wget -q https://raw.githubusercontent.com/cnote-chain/CNOTE-MultiMN/main/profiles/cnote.mn
multimn profadd cnote.mn cnote
```
Now the cnote profile is saved and the downloaded file can be removed if you want: `rm -rf cnote.mn`

Stop the cnote Service:
```
cnote-cli stop
systemctl stop cnote
```
Now create 3 extra instances with bootstrap (Ex. You want to make 3 Masternode):
```
multimn install cnote --bootstrap
multimn install cnote --bootstrap
multimn install cnote --bootstrap
```
You can check every MN like this:
```
cnote-cli-1 getinfo
cnote-cli-2 getinfo
cnote-cli-3 getinfo
cnote-cli-all getinfo
```
There's also a `cnote-cli-0`, that is a reference to the 'main node', not a created one with multimn.

Now, if you want to uninstall any instances,then just uninstall it with:

`multimn uninstall cnote 2` (Uninstall instance 2)

You can uninstall all of them with:

`multimn uninstall cnote all`


You can get priv key of all MN with:

`multimn list cnote --privkey`


Note this all priv key and make `masternode.conf` in your Coldwallet where you done MasternodeTx.
so `masternode.conf` look like this:
```
MN0 IP:24860 MN_PrivKey Tx_Hash Output_Index
MN01 IP:24860 MN_PrivKey Tx_Hash Output_Index
MN02 IP:24860 MN_PrivKey Tx_Hash Output_Index
MN03 IP:24860 MN_PrivKey Tx_Hash Output_Index
```

Here IP and port Same for all MN.

MN0 is your main_node MN which you create with <a href="https://github.com/cnote-chain/CNOTE-MN/blob/main/CNOTE-MN-v2.sh">CNOTE-MN-v2.sh</a> script.

MN01, MN02, MN03 is your masternode which you create with multimn.


Now StartMasternode from Coldwallet with:
```
startmasternode alias false MN0
startmasternode alias false MN01
startmasternode alias false MN02
startmasternode alias false MN03
```

Now StartMasternode in VPS with Service:

`systemctl start cnote` (Start your MN which is create with main_node <a href="https://github.com/cnote-chain/CNOTE-MN/blob/main/CNOTE-MN-v2.sh">CNOTE-MN-v2.sh</a> script)

Below 3 MN which is create with `multimn` script.
```
systemctl start   cnote-1.service
systemctl start   cnote-2.service
systemctl start   cnote-3.service
```

That's all now your MN start.