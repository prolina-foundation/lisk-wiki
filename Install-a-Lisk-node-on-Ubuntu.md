This tutorial is an alternative to the outdated source installation instructions on https://lisk.io/documentation/.

This was tested using Ubuntu 18.04 LTS but should work very similar in other versions of Ubuntu.

## Lisk node setup

```
$ sudo apt-get update && sudo apt-get upgrade -y
$ sudo apt-get install -y curl git npm libtool autoconf libpq-dev postgresql jq
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
$ ./lisk.sh rebuild -u https://testnet-snapshot.mylisk.com -f blockchain.db.gz
```

this takes a cuple of minutes. Now you can verify that lisk is syncing:

```
$ ./lisk.sh status
√ Lisk is running as PID: 16797
Current Block Height:  4565429
```

or watch the logs:

```
tail -f logs/lisk.log
```