---
title: 'ModSecurity 2.9.x in 5 minutes'
date: Wed, 01 Mar 2017 04:18:17 +0000
draft: false
tags: [apache, httpd, ModSecurity, Uncategorized]
---

There are just a few fundamental pieces to learn in order to get started with ModSecurity.

*   Phases
*   Actions and Rules
*   Collections

Phases
------

There are 5 phases of request processing in ModSecurity [1](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#Processing_Phases). You can hook into any one these phases using the `phase` keyword when writing ModSecurity Actions and Rules. The 5 phases occur in this order:

1.  Request Headers Phase
2.  Request Body Phase
3.  Response Headers Phase
4.  Response Body Phase
5.  Logging Phase

As you pass through the phases, you can still access the information from the previous phases. So, if you are not sure where to begin, start with Logging Phase 5.

Actions and Rules
-----------------

ModSecurity configurations are built around Actions (`SecAction`) and Rules (`SecRule`). We’ll start with `SecAction` since it is the same as a `SecRule`, just without a conditional. In other words, `SecAction` will always execute a list of actions, while a `SecRule` will only execute a list of actions if a certain condition is met.

### Example

    SecRuleEngine On
    SecAction id:100,phase:1,deny
    

`SecRuleEngine` enables the ModSecurity engine. If you do not enable it, none of your actions/rules will apply. The `SecAction` above is about as simple as it gets. It always denies a request, sending a 403 status to client. The `id:100,phase:1,deny` is known as the action list. Let’s work through it now. Every action _must_ include an `id` and it must be unique amongst all your `SecAction` and `SecRule`[2](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#id). After the `id` is the `phase:1` which means this rule will run in the Request Headers phase. Finally, the `deny` is the part that actually says this `SecAction` will send a 403 status.

#### Modified Example

If you want to send something besides the default deny status of 403, you can change it by using the `status` action like this:

    SecAction id:100,phase:1,deny,status:451
    

The action above would deny the client access and send a “451 Unavailable For Legal Reasons” status.

### Action List Order

The ordering of items in an action list is not always important. If you are using things like Transformation Functions [3](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#transformation-functions) (e.g. base64Encode, hexEncode, trim, etc) you need to pay attention to order, especially when you use multiple Transformation Functions in a single action or rule. Transformation Functions will change the input for the following functions and actions. The example from the previous section does not have any actions that are sensitive to ordering. For instance, the following would execute in exact same manner as in previous section:

    SecAction status:451,deny,phase:1,id:100
    

### More Useful Example

In the previous sections we used an example that simply denies every request. This isn’t very useful so we will add a condition to the action, making it a `SecRule`. The condition will be this: if someone requests the page and is from the IP address 192.168.100.200, then we want to deny them with the 451 status:

    SecRule REMOTE_ADDR "192\.168\.100\.200" id:100,phase:1,deny,status:451
    

The `REMOTE_ADDR` [4](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#remote_addr) is the value we are testing and the “192.168.100.200” is value that needs to match. If the test passes then the action list is executed.

Collections
-----------

Collections (a.k.a. Persistent Storage) are an important feature of ModSecurity because they give you the ability to store information over multiple requests. There are a few different types of collections[5](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#Persistent_Storage) but we will focus on GLOBAL and SESSION collections in this guide. A GLOBAL collection is not associated with any single request, session, or anything really; it is just a general collection that can be accessed from anywhere. A SESSION collection is different in that it is associated with a specific session and initialized differently than a GLOBAL collection. Let’s take a look at some example configuration.

### Global Collection Example

    SecDataDir /var/cache/mod_security/
    SecAction id:150,phase:1,initcol:reindeer=rudolph
    SecAction id:152,phase:1,setvar:reindeer.antlers=+2
    SecAction id:153,phase:1,setvar:reindeer.hoofs=+4
    

`SecDataDir` is the directory where the persistent database files will be kept. Make sure this is writable by your Apache HTTPD user. The first `SecAction` initializes the collection (`initcol` stands for initialize collection). The last two `SecActions` are setting the variables inside of the collection. So, in the example above, the collection is called `rudolph` and and it has two attributes: `antlers` and `hoofs`. The `antlers` are incremented by 2 for each request and the `hoofs` are incremented by 4 for each request. The collection has a structure like this:

    File name: reindeer
    rudolph => {
        antlers: 2,
        hoofs: 4
    }
    

Something imporatnt to note is that you can only call `initcol` once in your configuration since it is a GLOBAL collection. However, you can use `SecRule` in combination with `initcol` in the event you need to conditionally initialize a GLOBAL collection to use throughout a request’s lifecycle. For example, you can do:

    SecRule REQUEST_URI rudolph id:150,phase:1,initcol:reindeer=rudolph
    SecRule REQUEST_URI dancer id:151,phase:1,initcol:reindeer=dancer
    SecAction id:152,phase:1,setvar:reindeer.antlers=+2
    

### Session Collection Example

Instead of tracking things globally, you may want to associate a counter or variable with a specific session or request cookie. Let’s track how many people send requests from a specific session ID.

    SecAction id:154,phase:1,setsid:%{REQUEST_COOKIES.SESSIONID}
    SecAction id:155,phase:1,setvar:SESSION.requests=+1
    

In the configuration above, a counter called `requests` will be incremented for each request from the user with a specific session ID. The collection will have a structure like this:

    File name: default_SESSION
    e0d47ef20 => {
      requests: 100
    },
    a303b3cc7 => {
      requests: 93
    },
    146f5a98e => {
      requests: 174
    }
    

`e0d47ef20`, `a303b3cc7`, and `146f5a98e` are just example session ID’s which are the “keys” for each collection. After reading this article, you will be ready to get started with writing your own rules and actions for ModSecurity. Maybe even dive into the complex set of prebuilt rules in the OWASP ModSecurity Core Rule Set [6](https://github.com/SpiderLabs/owasp-modsecurity-crs/). References:

1.  [https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#Processing_Phases](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#Processing_Phases)
2.  [https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#id](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#id)
3.  [https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#transformation-functions](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#transformation-functions)
4.  [https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#remote_addr](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#remote_addr)
5.  [https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#Persistent_Storage](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#Persistent_Storage)
6.  [https://github.com/SpiderLabs/owasp-modsecurity-crs/](https://github.com/SpiderLabs/owasp-modsecurity-crs/)