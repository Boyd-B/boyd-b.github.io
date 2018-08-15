---
layout: post
title:  "Install SolrCloud With Zookeeper for production | C# Extension"
date:   2018-4-14 11:59
categories: csharp extension
image: https://picsum.photos/400/400/?rnd=414
---
Installing zookeeper for the first time is a real pain. The documentation is pretty straight forward but will not inform you how to deal with potential issues.

Download Zookeeper and Solr. I installed to C:/solrcloud

[Reference this guide, but don't start yet](https://lucene.apache.org/solr/guide/6_6/setting-up-an-external-zookeeper-ensemble.html)

First off, you'll notice you need 3 instances of zookeeper, but you'll use 2 instances of SolrCloud.

## Take a look at the zoo.cfg file

C:\solrcloud\zookeeper-3.4.6\conf\zoo.cfg

{% highlight  %}
    dataDir=/data/zookeeper
	clientPort=2181
	initLimit=5
	syncLimit=2
	server.1=sxp1-solr-a-01:2888:3888
	server.2=sxp1-solr-a-02:2889:3889
	server.3=sxp1-xdb-a-01:2890:3890
{% endhighlight %}

The biggest issue I had setting up zookeeper was that the ports it uses are not opened on the firewall. Once I did this, everything fell into place.

---
 - Open Start. . ...
 - Type windows firewall with advanced security into Start. ...
 - Click Windows Firewall with Advanced Security. ...
 - Enter your password if prompted. ...
 - Click Inbound Rules. ...
 - Click New Rule. ...
 - Check the "Port" option, then click Next. ...
 - Select TCP
 - Enter the Port ranges (each server will need it's own)
 - Server 1: 2181,2888,3888
 - Server 2: 2182,2889,3889
 - Server 3: 2183,2890,3890
---
One last thing to do is set the myid file according to dataDir=/data/zookeeper
Which maps to C:\data\zookeeper
Create a new text document, add the corresponding id (1,2,3) and remove the txt extension. Yes, it's really an extensionless file with a singler number in it.

At this point you can check zkcli and verify the connections. When ZooKeeper can't speak to the other instances in the ensemble then you will see a lot of the following error.

{% highlight  %}
Unable to read additional data from server sessionid 0x0, likely server has closed socket, closing socket connection and attempting reconnect
{% endhighlight  %}

Copy each server's zoo.cfg file to the solr server directory.
C:\solrcloud\solr-{version}\server\solr

Now you can install solrcloud. You can use the example (bin\solr -e cloud) to see it working.

Create a custom config and Upload to zookeeper:
- zkcli -zkhost {zkhost}:2181 -cmd upconfig -confdir C:\Solr\solr-6.6.5\server\solr\configsets\sitecore_configs\conf -confname sitecore

Create a new collection with your config
- bin\solr create -c custom_index -n sitecore -replicationFactor 2

With a replicationFactor set to 2, it will automatically replicate your new index to your two SolrCloud instances.

**Cheers!**


