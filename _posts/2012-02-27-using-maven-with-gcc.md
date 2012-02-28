---
layout: post
title: "Using Apache Maven to control GCC"
category: 
tags: [integration tools]
---
{% include JB/setup %}

Hitesh Shah posted a
[fix](https://issues.apache.org/jira/secure/attachment/12516199/MR-3922.1.patch)
to <https://issues.apache.org/jira/browse/MAPREDUCE-3922> that
illustrates how Maven can pass compiler flags to GCC:

<iframe width="95%" src="https://issues.apache.org/jira/secure/attachment/12516199/MR-3922.1.patch">

</iframe>

From the first line of the patch, we see how to tell Maven to create a
Maven-internal property `P` and then enable access to set that `P`
from the shell by using `-DP`.  In this case, the patch adds the
ability to set a Maven-internal property called
`container-executor.additional_cflags`, from the shell by using
`-Dcontainer-executor.additional_cflags`. For example, we can do:

    mvn clean install package -DskipTests -Dtar -Dcontainer-executor.additional_cflags="-m32"  -Pdist,native 

The second line of the patch changes the pom.xml at the value stored at the XPath:

    /project/profiles/profile[id='native']/build/plugins/plugin[artifactId='make-maven-plugin']/configuration/configureEnvironment/property/[name='CFLAGS']/value

so that Maven will look up the value of the Maven property
`${container-executor.additional_cflags}` and the value to the CFLAGS
(the command-line arguments that will be passed to GCC).

For example, if we do:

    mvn clean install package -DskipTests -Dtar -Dcontainer-executor.additional_cflags="-m32"  -Pdist,native 

Then the `container-executor` binary will be created with GCC using the `-m32` flag, (which will generate a 32-bit binary).
