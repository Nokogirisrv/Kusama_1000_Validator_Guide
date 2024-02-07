# Setting up a Kusama Node
## Requirements
- Validators must never have been slashed
    - Validators have a minimum stash in their accounts (10 KSM)
    - Validator nodes connect to a dedicated telemetry
    - Validators charge no more than 15% commission
    - Validators must have a separate Stash and Controller account
    - A one-week monitoring period to ensure validator nodes have stable operations. 
    - Validator stash addresses must get a verified on chain identity.
    - All nodes must upgrade to the latest version within 12 hours of its release if it is labeled "critical" or "high" priority and 24 hours if it is labeled "medium" or "low" priority.
    - Validator must operate nodes themselves, they may not be run by a third party or staking provider
(together the "Requirements").

Depending on the volume of applicants, Validators with less than 10 KSM self staked will still be considered on a case-by-case basis.

## Node Installation
```bash
mkdir -p $HOME/.kusama
chown -R $(id -u):$(id -g) $HOME/.kusama
ufw allow 30533
```
## Important Note - BEEFY Enabled on Westend and Kusama
[GitHub Pull Request #7661](https://github.com/paritytech/polkadot/pull/7661)
BEEFY is a consensus protocol that facilitates connection to Ethereum or Kusama <> Polkadot bridging. Currently, BEEFY is enabled by default and conflicts with sync warp when the --validator flag is active.
Validators using sync warp for Kusama and Westend need to disable BEEFY by adding the --no-beefy flag or removing the --sync=warp flag.
# Running Docker with Validator Name <moniker>:
```bash
docker run -dit \
--name kusama_node \
--restart always \
--network host \
-v $HOME/.kusama:/data -u $(id -u ${USER}):$(id -g ${USER}) \
parity/polkadot:latest --base-path /data --chain kusama \
--validator --name "<moniker>" \
--sync=warp \
--port 30533 \
--rpc-port 9953 \
--prometheus-port 9655 \
--in-peers 32 --out-peers 8 \
--telemetry-url 'wss://telemetry.polkadot.io/submit/ 1' \
--telemetry-url 'wss://telemetry-backend.w3f.community/submit 1' \
--no-beefy
```

# Validator Configuration

After your node has synchronized, retrieve the key from your node using the following command. If your node is not on the standard ports, adjust the RPC port at the end of the command.
```bash
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9953
```
If you receive a result similar to the following: {"jsonrpc":"2.0","result":"0xa0very0long0hex0string","id":1}, everything is fine. Copy the bolded key; you'll need it shortly.

Check if the keys have been created:
```bash
ls -a $HOME/.kusama/chains/ksmcc3/keystore/
```
Go to the [Staking page](https://polkadot.js.org/apps/#/staking/actions) and select Stash and Controller accounts
# Updating (Manually)
To manually update your node:

1. Update the Docker image:

    ```bash
    docker pull parity/polkadot
    ```

2. Stop the node:

    ```bash
    docker stop kusama_node
    ```

3. Remove the container:

    ```bash
    docker rm kusama_node
    ```
4. Download snapshot (optional):
   
   [https://snapshots.polkachu.com/snapshots/kusama](https://snapshots.polkachu.com/snapshots/kusama)
    ```
    curl -o - -L https://snapshots.polkachu.com/snapshots/kusama/kusama_16151324.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.kusama/chains/ksmcc3/
    ```
4. Start the container:

    ```bash
    docker run -dit \
    --name kusama_node \
    --restart always \
    --network host \
    -v $HOME/.kusama:/data -u $(id -u ${USER}):$(id -g ${USER}) \
    parity/polkadot:latest --base-path /data --chain kusama \
    --validator --name "<moniker>" \
    --sync=warp \
    --port 30533 \
    --rpc-port 9953 \
    --prometheus-port 9655 \
    --in-peers 32 --out-peers 8 \
    --telemetry-url 'wss://telemetry.polkadot.io/submit/ 1' \
    --telemetry-url 'wss://telemetry-backend.w3f.community/submit 1' \
    --no-beefy
    ```
# Useful Commands
To view logs:
```bash
docker logs kusama_node -fn 100
```
To restart the node:
```bash
docker restart kusama_node
```
# Node Deletion
```bash
docker stop kusama_node
docker rm kusama_node
cd $HOME
rm -rf .kusama/
```
# Useful Links
TelegramBot: <https://t.me/subvt_kusama_bot>
# Leaderboards
<https://vegas1kv.com/>

<https://1k.hirish.net/kusama>

<https://thousand-validators.kusama.network/#/leaderboard>
# Monitoring
<https://kusama.w3f.community/score/D5khA3qGvd8SDXepSrCGmYRWbNUzdJpjEyg6m1mFT7VtHpw>

<https://insights.math-crypto.com/kusama/D5khA3qGvd8SDXepSrCGmYRWbNUzdJpjEyg6m1mFT7VtHpw>

<https://kusama.polkastats.io/validator/D5khA3qGvd8SDXepSrCGmYRWbNUzdJpjEyg6m1mFT7VtHpw>

<https://kusama.w3f.community/score/DwZmVxujvVZmzmLZJ3wNTqyxBYTPDstCxayK6nwSR9HC1tS>

<https://insights.math-crypto.com/kusama/DwZmVxujvVZmzmLZJ3wNTqyxBYTPDstCxayK6nwSR9HC1tS>

<https://kusama.polkastats.io/validator/DwZmVxujvVZmzmLZJ3wNTqyxBYTPDstCxayK6nwSR9HC1tS>
