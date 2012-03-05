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

