# Traefik Elasticsearch

Before you start it is good to already enable some ports for both the swarm to be able to work and for the service, this step is very important for the services to run perfectly.

You will have to enable the ports of the machines that will be part of your cluster.

 Port         | UDP       | TCP       | Service                          |
|-------------|-----------|-----------|----------------------------------|
|80           | Yes       | Yes       |Http                              |
|443          | Yes       | Yes       |Https                             |
|2377         | Yes       | Yes       |Cluster management communications |
|4789         | Yes       | Yes       |Overlay network traffic           |
|5601         | Yes       | Yes       |Kibana                            |
|7946         | Yes       | Yes       |Communication among nodes         |
|8080         | Yes       | Yes       |Traefik                           |
|9200         | Yes       | Yes       |Elasticsearch                     |
|9300         | Yes       | Yes       |Elasticsearch                     |

After enable the ports, we will increase the number of virtual memory mappings

```bash
sysctl -w vm.max_map_count=262144
```

For you to run this service you will need to have 3 nodes running, only 1 needs to be a manager

You need to start swarm and put two other machines in the node group

Run this command below to be able to check

```bash
docker node ls
```

You should expect it to return

```response
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
nnc23sjm0nhbrc2wyznt4rz3l *   node1               Ready               Active              Leader              19.03.6
fcrpjwwjw60cpr487sc01dqr7     node2               Ready               Active                                  19.03.6
m8sudulzg66adg0qawl63206t     node3               Ready               Active                                  19.03.6
```

After your 3 nodes are running, we will have to add some labels to them

## Labels

These labels are very important for the service to run

**traefik**: enable the traefik service to run this node.

**master***: enable the service data can be established on that node.

**data***: enable the service master can be established on that node.

There are the location and values that you will have to pass to the label

|node1  | Key     | Value   |
|-------|---------|---------|
|       | traefik | true    |
|       | master1 | true    |
|       | data1   | true    |

|node2  | Key     | Value   |
|-------|---------|---------|
|       | traefik | true    |
|       | master2 | true    |
|       | data2   | true    |

|node3  | Key     | Value   |
|-------|---------|---------|
|       | traefik | true    |
|       | master3 | true    |
|       | data3   | true    |

Below is the command to which you can add the labels

```bash
docker node update --label-add KEY:VALUE NODE
```

After running the command we will check the existence of these labels

```bash
docker node ls -q | xargs docker node inspect   -f '{{ .ID }} [{{ .Description.Hostname }}]: {{ .Spec.Labels }}'
```

You should expect it to return

```response
9zv3fj26pts72phivn944qtwd [node1]: map[data1:true master1:true traefik:true]
w78xb8p31k6fjj6kujs0sgaqh [node2]: map[data2:true master2:true traefik:true]
lyxn9ebfyryp6t3sm84yaeobm [node3]: map[data3:true master3:true traefik:true]
```

After that we can start the services.

Let's start the elasticsearch service first

```bash

```

This command will start traefik which is our proxy

```bash
docker stack deploy -c stack-proxy.yml proxy
```



