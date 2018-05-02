This tutorial is an alternative to the outdated source installation instructions on [https://lisk.io/documentation/](https://lisk.io/documentation/).

This was tested using Ubuntu 18.04 LTS but should work very similar in other versions of Ubuntu.

## Lisk node setup

```
$ sudo apt-get update && sudo apt-get upgrade -y
$ sudo apt-get install -y curl git npm libtool autoconf automake build-essential libpq-dev postgresql jq
$ sudo mkdir /workspace
$ sudo chown $USER:$USER /workspace
$ cd /workspace
$ git clone https://github.com/LiskHQ/lisk.git
$ git clone https://github.com/LiskHQ/lisk-scripts.git
$ cd lisk
$ git checkout testnet-master
$ npm install
$ sudo -u postgres createuser --createdb $USER
$ psql -qd postgres -c "ALTER USER $USER WITH PASSWORD 'password';"
$ ln -s ../lisk-scripts/packaged/shared.sh .
$ ln -s ../lisk-scripts/packaged/env.sh .
$ ln -s ../lisk-scripts/packaged/lisk.sh .
$ ln -s ../lisk-scripts/packaged/etc .
$ chmod +x ../lisk-scripts/packaged/lisk.sh
$ sudo npm install -g pm2
```

## Sync blockchain

```
$ wget https://testnet-snapshot.mylisk.com/blockchain.db.gz 
$ ./lisk.sh rebuild -f blockchain.db.gz
```

this takes a couple of minutes. Now you can verify that Lisk is syncing:

```
$ ./lisk.sh status
√ Lisk is running as PID: 16797
Current Block Height:  4565429
```

or watch the logs:

```
tail -f logs/lisk.log
```

## Create a service

The Lisk node is only running because `./lisk.sh rebuild` keeps it running. As soon as you restart the machine, the node will be down. PostgreSQL however is installed as a system service and started automatically.

To let the Lisk node start together with the computer, we create the file `/etc/systemd/system/lisk-node.service` with

```
[Unit]
Description=Lisk Node
Requires=postgresql.service

[Service]
Type=oneshot
RemainAfterExit=true
User={{ user }}
Group={{ user }}
ExecStart=/workspace/lisk/lisk.sh start_node
ExecStop=/workspace/lisk/lisk.sh stop_node

[Install]
WantedBy=multi-user.target
```

Make sure to replace {{ user }} with the user name you are logged in with. Now you enable and start the Lisk node service:

```
$ sudo systemctl enable lisk-node
$ sudo systemctl start lisk-node
```

Verify the running state using

```
$ systemctl status lisk-node
$ /workspace/lisk/lisk.sh logs
```

That's it! You have a running Lisk node that syncs with the network.