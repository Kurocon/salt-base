Base Salt configuration
=======================

The main salt configuration repository. All server configuration is saved here so servers can be easily rebuilt from the config.

To pull the latest changes from the git repository's master branch, then enforce a highstate, run:
```bash
sudo salt-run fileserver.update && sudo salt --state-output=mixed '*' state.highstate
```

To specify a minion to update, replace the '*' with the minion's name or a grain, given by the 'salt-key -L' command. E.g:
```bash
sudo salt-run fileserver.update && sudo salt --state-output=mixed 'server1' state.highstate
```

To get verbose output for all states, instead of only the failures, remove "--state-output=mixed" from the command.

To list all minions that are accepted or are pending:
```bash
salt-key -L
```

To accept a minion key:
```bash
salt-key -a name*
```


Setting up a new minion
-----------------------
Add SaltStack repository and install salt-minion:
```bash
apt-get update
apt-get install -y wget gnupg
echo "deb http://repo.saltstack.com/apt/debian/9/amd64/latest stretch main" > /etc/apt/sources.list.d/salt.list
wget -O - https://repo.saltstack.com/apt/debian/9/amd64/latest/SALTSTACK-GPG-KEY.pub | apt-key add -
apt-get update
apt-get install -y salt-minion
```
Configure the salt master address in /etc/salt/minion and restart the minion:
```bash
HSTNME=`echo "\`hostname\`" | tr '[:upper:]' '[:lower:]'`
echo "master: 'saltmaster'
id: '$HSTNME'" > /etc/salt/minion
systemctl restart salt-minion
```

On the salt-master accept the new minion by its key:
```bash
sudo salt-key --list-all
sudo salt-key --accept=<key>
```

Test your minion connection:
```bash
sudo salt '<minion name>' test.ping
```

