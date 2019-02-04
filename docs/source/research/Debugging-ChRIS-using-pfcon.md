## Using pfioh with mount-dir and pman with swarm

### APIs (swarm)
 1. pfioh: mount-dir
 1. pman: swarm
 1. pfcon
 1. pfurl (curl like api)

### Staring the services (swarm)
pfioh startup
```shell
    bin/pfioh --forever --httpResponse --storeBase=/tmp/share --createDirsAsNeeded
```

pman startup
```shell
    bin/pman --rawmode=1 --http --port=5010 --listeners=12
```

pfcon startup
```shell
    bin/pfcon --forever --httpResponse
```

Add pfioh and pman IPs in `pfcon.py` 
```shell
Gd_internalvar service:'moc'
```

Run the following command from pfurl
```shell
    bin/pfurl --verb POST --raw --http <pfcon ip:port>/api/v1/cmd --httpResponseBodyParse --jsonwrapper 'payload' --msg '
           {
       "action":"coordinate",
       "threadAction":true,
       "meta-store":{
          "meta":"meta-compute",
          "key":"jid"
       },
       "meta-data":{
          "remote":{
             "key":"%meta-store"
          },
          "localSource":{
             "path":"/tmp/large_datadir"
          },
          "localTarget":{
             "path":"/tmp/large_localstore",
             "createDir":true
          },
          "specialHandling":{
              "op":"plugin",
              "cleanup":true
          },
          "transport":{
             "mechanism":"compress",
             "compress":{
                "encoding":"none",
                "archive":"zip",
                "unpack":true,
                "cleanup":true
             }
          },
          "service":"moc"
       },
       "meta-compute":{
          "cmd":"$execshell $selfpath/$selfexec --prefix test- --sleepLength 0 /share/incoming /share/outgoing",
          "auid":"rudolphpienaar",
          "jid":"sa458",
          "threaded":true,
          "container":{
             "target":{
                "image":"fnndsc/pl-simpledsapp",
                "cmdParse":true
             },
             "manager":{
                "image":"fnndsc/swarm",
                "app":"swarm.py",
                "env":{
                   "meta-store":"key",
                   "serviceType":"docker",
                   "shareDir":"%shareDir",
                   "serviceName":"sa458"
                }
             }
          },
          "service":"moc"
       }
    }'
```
---

## Using pfioh with swift and pman with OpenShift

### APIs (OpenShift)
 1. pfioh: swift-store
 1. pman: openshift
 1. pfcon
 1. pfurl (curl like api)

### Staring the services (OpenShift)
pfioh startup
```shell
    bin/pfioh --forever --httpResponse --swift-storage --createDirsAsNeeded
```

pman startup
```shell
    bin/pman --rawmode=1 --http --port=5010 --listeners=12 --container-env=openshift
```

pfcon startup
```shell
    bin/pfcon --forever --httpResponse
```

Add pfioh and pman IPs in `pfcon.py`
```shell
Gd_internalvar service:'moc'
```

Run the following command from pfurl
```shell
    bin/pfurl --verb POST --raw --http <pfcon ip:port>/api/v1/cmd --httpResponseBodyParse --jsonwrapper 'payload' --msg '
    {
       "action":"coordinate",
       "threadAction":true,
       "meta-store":{
          "meta":"meta-compute",
          "key":"jid"
       },
       "meta-data":{
          "remote":{
             "key":"%meta-store"
          },
          "localSource":{
             "path":"/tmp/large_datadir"
          },
          "localTarget":{
             "path":"/tmp/large_localstore",
             "createDir":true
          },
          "transport":{
             "mechanism":"compress",
             "compress":{
                "encoding":"none",
                "archive":"zip",
                "unpack":true,
                "cleanup":true
             }
    	      },
          "service":"moc"
       },
       "meta-compute":{
          "cmd":"$execshell $selfpath/$selfexec --prefix test- --sleepLength 0 /share/incoming /share/outgoing",
          "auid":"rudolphpienaar",
          "jid":"os572",
          "threaded":true,
          "container":{
                "target":{
                   "image":"fnndsc/pl-simpledsapp",
                   "cmdParse":false
                }
          },
          "service":"moc"
        }      
    }'
```
