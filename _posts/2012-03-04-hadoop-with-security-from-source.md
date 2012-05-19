---
layout: post
title: "Secure Hadoop From Scratch"
category: hadoop
tags: [hadoop, outofthebox]
---
{% include JB/setup %}

This is a short guide to getting started with building and running the
latest Hadoop, with security support, from source code. It builds on
[Hadoop From Source](/hadoop/2012/02/27/hadoop-from-source). This post is
meant to be self-contained, so you can just start from this, or if you
have trouble with it, you can look at [Hadoop From
Source](/hadoop/2012/02/27/hadoop-from-source) and see if that works better
for you. Let me know of any problems by emailing me
(ekoontz@hiro-tan.org) or filing a GitHub issue and/or a patch.

## Prerequisites

Prerequisites are the same as given by [Hadoop From Source] (2012-02-27-hadoop-from-source#Prerequisites), plus:

* [A working Kerberos install](#kerberos)
* [a working DNS environment](#dns)
* [Root access](#root)

## Kerberos 

I used MIT Kerberos for my setup. Athough I haven't had time to try
alternative Kerberos implementations such as:

* [Shishi](http://www.gnu.org/software/shishi/)
* [ApacheDS](http://directory.apache.org/)
* [Heimdal](http://h5l.org)

I would like to hear of anyone's experiences with them.

## DNS

Make sure that you can resolve hostnames correctly, both forward and
reverse (that is, if you configure Hadoop to use a host H, that H is
resolvable to the correct IP I, and that reverse DNS works too, so
that I reverse-DNS-resolves to H.

Also, you will need to add the following Java property in various
places below:
 
     -Dsun.net.spi.nameservice.provider.1=dns,sun

This serves to override Java's default name resolution
protocol 'sun', which seems to have problems when interacting with
Kerberos names that include hostnames
(e.g. service/hostname@REALM). 

In particular, if this property is not explicitly set, Java will try
to use the wrong IP for H. We will show symptoms of this behavior
below so that you can be aware of it and how to correct it if necessary.

To avoid such problems, however, please note below where this property
is mentioned as being required.

## Root

You will need root access for two reasons:

* Having root access allows you to run kadmin.local to add or modify
Kerberos principals, which will be necessary below.
* Starting the Hadoop datanode securely requires root access.
(though, why?)

# Secure core-site.xml

<script src="https://gist.github.com/1974868.js">

</script>




# Secure environment variables

    YARN_OPTS=-DHADOOP_JAAS_DEBUG=true \
              -Dsun.net.spi.nameservice.provider.1=dns,sun\
              -Djava.security.krb5.realm=HADOOP.LOCALDOMAIN \
              -Djava.security.krb5.kdc=mac.foofers.org 

    HADOOP_OPTS=-DHADOOP_JAAS_DEBUG=true \
              -Dsun.net.spi.nameservice.provider.1=dns,sun 
              -Djava.security.krb5.realm=HADOOP.LOCALDOMAIN \
              -Djava.security.krb5.kdc=mac.foofers.org


# Troubleshooting

You may see something like:

<script src="https://gist.github.com/1977116.js" style="height:5em">

</script>

Note the long pause in log message timestamps between `22:41:13` and
`22:42:44`. This indicates that the Kerberos server could not be
reached. Check to see that the hostname `H` given in your
`-Djava.security.kerb5.krc=H` is reachable: do `telnet H 88`. If this
succeeds, then the Kerberos server is probably working ok : the
problem is that Java is unable to resolve H to the correct IP. Check
to make sure that `-Dsun.net.spi.nameservice.provider.1=dns,sun` is
set.

For more on DNS and Java interaction, have a look at my
[hostname-testing Github
repository](https://github.com/ekoontz/hostname-testing), which has
some sample code to test interactions between Java and DNS.

# Related Material

[hostname-testing](https://github.com/ekoontz/hostname-testing), sample code
to test interactions between Java and DNS.

[jaas_and_kerberos](https://github.com/ekoontz/jaas_and_kerberos), sample code
to test interactions between Java, JAAS, and Kerberos.

[Up and Running With Secure
Zookeeper](https://github.com/ekoontz/zookeeper/wiki), another guide
to getting started with a sample Kerberos setup (and how to integrate
Zookeeper with it).

# Tips

In a terminal window, run the following and keep it where you can watch it:

    sudo tail -f /var/log/krb5kdc/kdc.log



