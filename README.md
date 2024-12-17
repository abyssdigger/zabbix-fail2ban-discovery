# Fail2Ban template for Zabbix (updated clone of [hermanekt](https://github.com/hermanekt/zabbix-fail2ban-discovery-))
### Features:

- Automatic discovery of jails
- Monitor service status
- Monitor jails
- Jails graph
- Banned IP list value with trigger _(added by abyssdigger)_

## Installation
### 1. Set configuration file
Download the latest version of configuration file `fail2ban.conf` from the [repo](https://github.com/abyssdigger/zabbix-fail2ban-discovery).
Put the file here `/etc/zabbix/zabbix_agentd.d/fail2ban.conf` or here for zabbix agent 2 `/etc/zabbix/zabbix_agentd2.d/fail2ban.conf`

Zabbix Agent
```console
wget https://raw.githubusercontent.com/abyssdigger/zabbix-fail2ban-discovery/master/fail2ban.conf -O /etc/zabbix/zabbix_agentd.d/fail2ban.conf
```
Zabbix Agent 2
```console
wget https://raw.githubusercontent.com//abyssdigger/zabbix-fail2ban-discovery/master/fail2ban.conf -O /etc/zabbix/zabbix_agent2.d/fail2ban.conf
```

### 2. Grant access to Fail2Ban
Fail2ban works only with `root` by default. We need to grant permission to Zabbix to access the Fail2ban by adding this 2 lines to `/etc/sudoers`:
```console
zabbix ALL=NOPASSWD: /usr/bin/fail2ban-client status
zabbix ALL=NOPASSWD: /usr/bin/fail2ban-client status *
```
Then apply new sudoers and zabbix agent setting
```console
/etc/init.d/sudo restart
/etc/init.d/zabbix-agent restart 
```
OR
```console
/etc/init.d/sudo restart
/etc/init.d/zabbix-agend restart
```
`If you have systemd, please use this correct command.`
```console
systemctl restart zabbix-agent
```
OR
```console
systemctl restart zabbix-agent2
```

### 3. Test Zabbix Agent setting

Zabbix Agent
```console
sudo -u zabbix zabbix_agent -c /etc/zabbix/zabbix_agent.conf -t fail2ban.discovery
#> fail2ban.discovery                            [s|{"data":[{"{#JAIL}":"proxmox-gui"}, {"{#JAIL}":"sshd"}]}]

sudo -u zabbix zabbix_agent -c /etc/zabbix/zabbix_agent.conf -t fail2ban.bancount['sshd']
#> fail2ban.bancount[sshd]                       [s|1]

sudo -u zabbix zabbix_agent -c /etc/zabbix/zabbix_agent.conf -t fail2ban.banlist['sshd']
#> fail2ban.banlist[sshd]                        [s|XXX.XXX.XXX.XXX]
```

Zabbix Agent 2
```console
sudo -u zabbix zabbix_agent2 -c /etc/zabbix/zabbix_agent2.conf -t fail2ban.discovery
#> fail2ban.discovery                            [s|{"data":[{"{#JAIL}":"proxmox-gui"}, {"{#JAIL}":"sshd"}]}]

sudo -u zabbix zabbix_agent2 -c /etc/zabbix/zabbix_agent2.conf -t fail2ban.bancount['sshd']
#> fail2ban.bancount[sshd]                       [s|1]

sudo -u zabbix zabbix_agent2 -c /etc/zabbix/zabbix_agent2.conf -t fail2ban.banlist['sshd']
#> fail2ban.banlist[sshd]                        [s|XXX.XXX.XXX.XXX]
```

The response above with list of jails means that everything works fine. 

### Configure the Zabbix Server
1. Import the template file into Zabbix Server (this operation is done only once).
2. Change the update Interval to what pleases you (default is 1 minute).
3. Add the template to your hosts.

Thanks to [hermanekt](https://github.com/hermanekt)
