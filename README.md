# TestElasticsearch

An Elasticsearch testing playground.

## Installation

### Bitnami Elasticsearch on AWS

1. Spin up an AWS EC2 instance by choosing the appropriate BitNami neo4j AMI from this list: https://bitnami.com/stack/elasticsearch/cloud/aws/amis

2. Download the .pem key.

3. git bash (install on computer if not already present) in the folder with the key and type:

```
chmod 400 keyname.pem
```

which gives the user permission to read the file (4) and no permissions (0) to the group and everyone else.

4. Connect to the aws instance using the following command:

```
ssh -i keyname.pem bitnami@aws_instance_public_dns
```

To remove the added ip from the known hosts list, use:

```
ssh-keygen -R server_ip_address
```

Unknown if elasticsearch requires any user and password.

To end the connection, enter:

```
exit
```
