# Multiple MasterNode

A script which is easily create Multiple Masternodes of the same coin in the same VPS.

First of all you have to start one masternode using <a href="https://github.com/cnote-chain/CNOTE-MN/blob/main/CNOTE-MN.sh">CNOTE-MN.sh</a> script that is your MainNode.

# Guide of use CNOTE-MN.sh for MainNode:

```
wget -q https://raw.githubusercontent.com/cnote-chain/CNOTE-MN/main/CNOTE-MN.sh
sudo chmod +x CNOTE-MN.sh
./CNOTE-MN.sh
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

MN0 is your main_node MN which you create with <a href="https://github.com/cnote-chain/CNOTE-MN/blob/main/CNOTE-MN.sh">CNOTE-MN.sh</a> script.

MN01, MN02, MN03 is your masternode which you create with multimn.


Now StartMasternode from Coldwallet with:
```
startmasternode alias false MN0
startmasternode alias false MN01
startmasternode alias false MN02
startmasternode alias false MN03
```

Now StartMasternode in VPS with Service:

`systemctl start cnote` (Start your MN which is create with main_node <a href="https://github.com/cnote-chain/CNOTE-MN/blob/main/CNOTE-MN.sh">CNOTE-MN.sh</a> script)

Below 3 MN which is create with `multimn` script.
```
systemctl start   cnote-1.service
systemctl start   cnote-2.service
systemctl start   cnote-3.service
```

That's all now your MN start.

***

# Guide to Update existing Nodes :

If you already have masternodes running on the old **v1** version, you can move all of them (the MainNode and every dupe created with `multimn`) to **v2.0.1** with the steps below.

**Important:** The `CNOTE-MN.sh` script is **not** a gentle in-place updater. When run on a server it performs a **clean reinstall of the MainNode**:

- it stops the old daemon and removes the old binaries from `/usr/local/bin/`, then installs the v2.0.1 daemon (this is good — every dupe shares this binary, so all nodes get the v2.0.1 daemon),
- it **regenerates** the MainNode config `~/.cnote/cnote.conf` (new RPC credentials and masternode settings),
- it **deletes the MainNode blockchain** (`blocks chainstate sporks evodb`) and applies a fresh bootstrap.

Your masternodes are **not** lost during this: a masternode lives on-chain through its collateral (the MasternodeTx in your Coldwallet), not on the VPS. As long as your private keys and MasternodeTx outputs are safe, you can always rebuild the VPS nodes and re-start the masternodes. Just follow these steps in order:

**Step 1 — Back up every private key first** (do this BEFORE running anything):
```
multimn list cnote --privkey
```
Save this output somewhere safe.

**Step 2 — Stop all the nodes:**
```
cnote-cli-all stop
systemctl stop cnote
systemctl stop cnote-1.service
systemctl stop cnote-2.service
systemctl stop cnote-3.service
```

**Step 3 — Run the v2 script to update the MainNode:**
```
wget -q https://raw.githubusercontent.com/cnote-chain/CNOTE-MN/main/CNOTE-MN.sh
sudo chmod +x CNOTE-MN.sh
./CNOTE-MN.sh
```

**Step 4 — Re-bootstrap the dupes** so they run the v2 chain format correctly:
```
multimn reinstall cnote 1 --bootstrap
multimn reinstall cnote 2 --bootstrap
multimn reinstall cnote 3 --bootstrap
```

**Step 5 — Update `masternode.conf` in your Coldwallet.** Because the MainNode config was regenerated, the MN0 private key may have changed. Check the current keys with `multimn list cnote --privkey` and update the matching lines in your Coldwallet's `masternode.conf`.

**Step 6 — Start the masternodes again from the Coldwallet:**
```
startmasternode alias false MN0
startmasternode alias false MN01
startmasternode alias false MN02
startmasternode alias false MN03
```

**Step 7 — Verify** every node is now on v2:
```
cnote-cli-all getinfo
```
Check the `version` / `protocolversion` field to confirm the update.

Also remember to keep the multimn script itself up to date:
```
multimn update
```

> **Tip:** Always note down your private keys (Step 1) before upgrading. As long as the keys and your MasternodeTx outputs are safe, you can always rebuild the nodes without losing the masternodes.