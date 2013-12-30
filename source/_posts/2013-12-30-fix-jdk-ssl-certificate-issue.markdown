---
layout: post
title: Fix the "Failure initializing default system SSL context" issue
date: 2013-12-30 11:19
comments: true
categories: jdk, java, ssl
---

Encountered this problem when I try to resolve dependencies for a gradle project, the root cause is a ssl certificate file can not be found in JDK installation.

Find below solution on internet:

``` bash
#!/bin/sh
set -e
#the location of certificate file which gradle asks for, create the directory if it's not existed.
cacerts=/Library/Java/JavaVirtualMachines/1.6.0_38-b04-436.jdk/Contents/Home/lib/security/cacerts

curl 'https://docs.codehaus.org/download/attachments/158859410/startssl-CA.pem?version=1&modificationDate=1277952972158' \
      | keytool -import -alias StartSSL-CA -file /dev/stdin -keystore $cacerts -storepass changeit -noprompt
curl 'https://docs.codehaus.org/download/attachments/158859410/startssl-Intermediate.pem?version=1&modificationDate=1277952972182' \
      | keytool -import -alias StartSSL-Intermediate -file /dev/stdin -keystore $cacerts -storepass changeit -noprompt
```

create a new file and paste the code into it, then run with sudoer permission.
