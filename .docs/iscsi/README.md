# How to: Raspberry Pi 4 as an iSCSI SAN (iSCSI Target)


## :cl: Install 

1. First we need to install the targetcli interface to configure the target.

As root (or use sudo) run the following [command :point_left:](INSTALL.md) if you’re running Ubuntu Server. 

## :abacus: Target Handling

- [ ] Introducing the iSCSI target `CLI`

type `exit` to quit the `CLI`

```
sudo targetcli
```
> Returns :
```yaml
targetcli shell version 2.1.53
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

/iscsi> exit
Global pref auto_save_on_exit=true
Configuration saved to /etc/rtslib-fb-target/saveconfig.json
```

:bulb: Note: the `/etc/rtslib-fb-target/saveconfig.json` config file

```
sudo cat /etc/rtslib-fb-target/saveconfig.json
```
> Returns :
```json
{
  "fabric_modules": [],
  "storage_objects": [],
  "targets": []
}
```

- [ ] `Create` a target

```
sudo targetcli
```
> Returns :
```yaml
targetcli shell version 2.1.53
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

/> cd iscsi
/iscsi> create
Created target iqn.2003-01.org.linux-iscsi.nunki.aarch64:sn.75fcaf1cfc33.
Created TPG 1.
Global pref auto_add_default_portal=true
Created default portal listening on all IPs (0.0.0.0), port 3260.
/iscsi> 
```

```
sudo cat /etc/rtslib-fb-target/saveconfig.json
```
> Returns :
```json
{
  "fabric_modules": [],
  "storage_objects": [],
  "targets": [
    {
      "fabric": "iscsi",
      "parameters": {
        "cmd_completion_affinity": "-1"
      },
      "tpgs": [
        {
          "attributes": {
            "authentication": 0,
            "cache_dynamic_acls": 0,
            "default_cmdsn_depth": 64,
            "default_erl": 0,
            "demo_mode_discovery": 1,
            "demo_mode_write_protect": 1,
            "fabric_prot_type": 0,
            "generate_node_acls": 0,
            "login_keys_workaround": 1,
            "login_timeout": 15,
            "netif_timeout": 2,
            "prod_mode_write_protect": 0,
            "t10_pi": 0,
            "tpg_enabled_sendtargets": 1
          },
          "enable": true,
          "luns": [],
          "node_acls": [],
          "parameters": {
            "AuthMethod": "CHAP,None",
            "DataDigest": "CRC32C,None",
            "DataPDUInOrder": "Yes",
            "DataSequenceInOrder": "Yes",
            "DefaultTime2Retain": "20",
            "DefaultTime2Wait": "2",
            "ErrorRecoveryLevel": "0",
            "FirstBurstLength": "65536",
            "HeaderDigest": "CRC32C,None",
            "IFMarkInt": "Reject",
            "IFMarker": "No",
            "ImmediateData": "Yes",
            "InitialR2T": "Yes",
            "MaxBurstLength": "262144",
            "MaxConnections": "1",
            "MaxOutstandingR2T": "1",
            "MaxRecvDataSegmentLength": "8192",
            "MaxXmitDataSegmentLength": "262144",
            "OFMarkInt": "Reject",
            "OFMarker": "No",
            "TargetAlias": "LIO Target"
          },
          "portals": [
            {
              "ip_address": "0.0.0.0",
              "iser": false,
              "offload": false,
              "port": 3260
            }
          ],
          "tag": 1
        }
      ],
      "wwn": "iqn.2003-01.org.linux-iscsi.nunki.aarch64:sn.75fcaf1cfc33"
    }
  ]
}
```

```
sudo targetcli ls
```
> Returns :
```yaml
o- / ....................................................................................................... [...]
  o- backstores ............................................................................................ [...]
  | o- block ................................................................................ [Storage Objects: 0]
  | o- fileio ............................................................................... [Storage Objects: 0]
  | o- pscsi ................................................................................ [Storage Objects: 0]
  | o- ramdisk .............................................................................. [Storage Objects: 0]
  o- iscsi .......................................................................................... [Targets: 1]
  | o- iqn.2003-01.org.linux-iscsi.nunki.aarch64:sn.75fcaf1cfc33 ....................................... [TPGs: 1]
  |   o- tpg1 ............................................................................. [no-gen-acls, no-auth]
  |     o- acls ........................................................................................ [ACLs: 0]
  |     o- luns ........................................................................................ [LUNs: 0]
  |     o- portals .................................................................................. [Portals: 1]
  |       o- 0.0.0.0:3260 ................................................................................... [OK]
  o- loopback ....................................................................................... [Targets: 0]
  o- vhost .......................................................................................... [Targets: 0]
```




 
 # References
 
 - [ ] [How to: Raspberry Pi 4 as an iSCSI SAN (iSCSI Target)](https://www.stephenwagner.com/2020/03/18/how-to-raspberry-pi-4-as-an-iscsi-san-iscsi-target)

