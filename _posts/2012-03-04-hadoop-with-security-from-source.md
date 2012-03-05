---
layout: post
title: "Secure Hadoop From Scratch"
category: hadoop
tags: [hadoop, outofthebox]
---
{% include JB/setup %}

This is a short guide to getting started with building and running the
latest Hadoop, with security support, from source code. It builds on
[Hadoop From Source](2012-02-27-hadoop-from-source). This post is
meant to be self-contained, so you can just start from this, or if you
have trouble with it, you can look at [Hadoop From
Source](2012-02-27-hadoop-from-source) and see if that works better
for you. Let me know of any problems by emailing me
(ekoontz@hiro-tan.org) or filing a GitHub issue and/or a patch.

## Prerequisites

Prerequisites are the same as given by [Hadoop From Source] (2012-02-27-hadoop-from-source#Prerequisites), plus:

* [#kerberos]A working Kerberos install 
* [#dns](a working DNS environment)
* [#root](Kerberos Admin permission)

## Kerberos 

I used MIT Kerberos for my setup. Athough I haven't had time to try
alternative Kerberos implementations:

* GNU 
* ApacheDS
* Heimdahl

I would like to hear of anyone's experiences with them.




## DNS

## Root
