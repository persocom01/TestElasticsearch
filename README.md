# TestElasticsearch

An Elasticsearch testing playground.

## Installation

### Elasticsearch on AWS

1. Spin up a Ubuntu 18.04 m4.large AWS EC2 and configure it to accept SSH and TCP 5601 (kibana) connections from anywhere.

2. Download the .pem key.

3. git bash (install on computer if not already present) in the folder with the key and type:

```
chmod 400 keyname.pem
```

which gives the user permission to read the file (4) and no permissions (0) to the group and everyone else.

4. Connect to the aws instance using the following command:

```
ssh -i keyname.pem ubuntu@aws_instance_public_dns
```

To remove the added ip from the known hosts list, use:

```
ssh-keygen -R server_ip_address
```

To end the connection, enter:

```
exit
```

5. Install elasticsearch.

Before ending the connection, we need to actually install elasticsearch on the machine. Do so by running the following commands:

```
sudo apt-get update
sudo apt-get install default-jre
```

You may now check your java version using: java -version

```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

Gets a key from the url then adds it to the list of keys used by apt to authenticate packages. -qO - stands for -quiet and -Output filename(-).

```
sudo apt-get install apt-transport-https
```

Installs the apt-transport-https package.

```
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

Takes the echo string and -a appends it to the repository definition in the system. WARNING: running this command more than once will result in duplicate entries you may have to manually remove later.

```
sudo apt-get update && sudo apt-get install elasticsearch
```

Installs elasticsearch. && just combines two commands into one. They can be run seperately.

6. Start elasticsearch

How elasticsearch is started depends on whether the system uses SysV init or systemd. To find out, enter the following command in aws:

```
ps -p 1
```

The response should look something like this:

```
PID TTY          TIME CMD
    1 ?        00:00:16 systemd
```

As it is systemd, use the following commands to configure kibana to start upon system boot:

```
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
```

kibana can then be started or stopped with the following commands:

```
sudo systemctl start elasticsearch.service
sudo systemctl stop elasticsearch.service
```

Verify that elasticsearch is running by using:

```
curl http://localhost:9200
```

It also gives you the elasticsearch version number.

7. Install kibana

We normally need to install kibana for visualising data stored in elasticsearch. It has to be the same version of elasticsearch. If you installed elasticsearch on a fresh stack as defined above so far, installing kibana should be as simple as running the following command:

```
sudo apt-get install kibana
```

8. Run kibana.

As with elasticsearch, for systemd, use the following commands to configure kibana to start upon system boot:

```
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable kibana.service
```

kibana can then be started or stopped with the following commands:

```
sudo systemctl start kibana.service
sudo systemctl stop kibana.service
```

To see if kibana was started or not, the log files can be viewed using the following command:

```
journalctl -u kibana.service --since "YYYY-MM-DD hh:mm:ss"
```

Time being option on first start since displaying all logs is good enough. Use ctrl c to exit.

9. Configure kibana.

Before kibana can be accessed from a remote host, it needs to be configured. To do so, run the following command on debian or ubuntu systems (it can also be seen normally by first using cd / to navigate to the root directory):

```
sudo vim /etc/kibana/kibana.yml
```

Go to edit mode by pressing i then uncomment and set the following line:

```
server.host: "0.0.0.0"
```

0.0.0.0 will cause kibana to be accessible from any ip, so it's practically unsecured (default localhost). Security will be another matter, but for now, exit insert mode by pressing esc, save using :w and exit using :q. :q! exits without commiting unsaved changes. Now restart kibana using:

```
sudo systemctl restart kibana.service
```

## Issuing commands

The kibana web interface can be access through the browser via the address: http://aws_instance_public_dns_or_ip:5601

While connected to the web interface, the management (lowest icon) on the bottom right will allow you to manage indices in elasticsearch and index patterns in kibana. The latter is imported because deleted indices will still retain their index patterns in kibana.

Alternatively, creating or deleting indices can also be handled while connected to the ec2 server via ssh, or using the dev tools console in kibana. The following commands will be the ec2 command followed by the kibana console equivalent. Note though, that pasting the ec2 commands into kibana will automatically convert them to their console equivalent.

1. To determine the version of elasticsearch enter the following command while connected to the ec2 server:

```
curl http://localhost:9200
```

2. List all indices:

```
curl -XGET http://localhost:9200/_cat/indices?v
```

3. To delete an index, use:

```
curl -XDELETE http://localhost:9200/index_name
```

4. To delete all indices:

```
curl -XDELETE http://localhost:9200/_all
```

5. To delete a document:

```
curl -XDELETE http://localhost:9200/index_name/type_name/document_id
```
