---
layout: post
title:  "Configure MacOS-X High Sierra sendmail"
date:   2018-07-12 14:54:21 -0300
categories: [recipe]
tags: [macosx, el capitan, facetime]
author: Marcelo Foss
---

# SENDMAIL on MAC OSX Yosemite

#### http://www.garron.me/en/mac/postfix-relay-gmail-mac-os-x-local-smtp.html

1) Create a file to store our credentials:

    sudo vim /etc/postfix/sasl_passwd

2) Add something like this:

    smtp.gmail.com:587 username@gmail.com:password

3) Now run:

    sudo postmap /etc/postfix/sasl_passwd

4) Prepare the postfix main config file:

    sudo vim /etc/postfix/main.cf

5) Add/update these lines

    relayhost=smtp.gmail.com:587
    smtp_sasl_auth_enable=yes
    smtp_sasl_password_maps=hash:/etc/postfix/sasl_passwd
    smtp_use_tls=yes
    smtp_tls_security_level=encrypt
    tls_random_source=dev:/dev/urandom
    smtp_sasl_security_options = noanonymous
    smtp_always_send_ehlo = yes
    smtp_sasl_mechanism_filter = plain

6) Stop/Start the service

    sudo postfix stop && sudo postfix start

7) Check the queue for any errors

    mailq
