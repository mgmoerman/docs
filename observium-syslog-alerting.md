[link_obs_syslog]: http://www.observium.org/docs/syslog/
[link_obs_transports]: http://www.observium.org/docs/alerting_transports/

##Observium SYSLOG alerting

Observium has builtin syslog alerting since release r7970. This text will explain on what it does, how it works, and how you configure it.

Something that was missing in the entity based alerting system, is now fixed with this brand new feature of Observium. 

You can now perform realtime alerting where as the conventional entity based alerting only would trigger every 5 minutes, because it is poller based. 

Now this new feature is not going to replace the existing entity alerting, as it just serves a different purpose, as it just allows you to catch issues from your devices, which are not possible to catch with the existing entity based alerting system. Examples are: Duplicate IPs/MAC addresses, or OSPF messages.

Syslog alerting allows you to send out notifications from syslog messages that are produced by your devices. You can match on those with Regular Expressions

Syslog alerting in observium perfectly integrates with the existing contact system, so it allows you to notify via the usual channels, E-mail, Slack, Pagerduty, XMPP, webhook, etc.

For a complete overview of transport methods, see: [Alerting Transports][link_obs_transports]



### How to set it up

First make sure you have configured syslog to integrate with Observium. The documentation for doing this, can be found here: [Syslog Integration][link_obs_syslog]

If you are running r7970 or later you will find 2 new entries in the global menu:
 
 * Syslog Alerts
 * Syslog Rules

![screenshot1](http://www.maartenmoerman.nl/screenshots/e8c53ef8e1030a2a83ecbffa.png)

Let's start with creating a useful syslog alert rule, that triggers an alert when there is a duplicate mac address found on a Cisco device:

 * First click on "Syslog Rules" in the global menu
 * Then click on "Add Syslog Rule"

![screenshot2](http://www.maartenmoerman.nl/screenshots/34b076950eed0f290c4b524c.png)

You will then be presented with the following screen, where you have to configure the details of the syslog alert rule:

 * Rule Name: This defines the name for the actual rule, this is just an administrative reference
 * Message: This is the message that will end up in the notification send out
 * Regular Expression: This is where you configure the actual rule to match syslog content against
 
 
![screenshot3](http://www.maartenmoerman.nl/screenshots/a8893b6b623cb9f01be4a0d3.png)


Syslog Rules are built using standard PCRE regular expressions.

There are many online resources to help you learn and test regular expressions. Good resources include regex101.com, Debuggex Cheatsheet, regexr.com and Tutorials Point. There are many other sites with examples which can be found online.

A simple rule to match the word "duplicate" could look like:

```/duplicate/```

A more complex rule to match SSH authentication failures from PAM for the users root or adama might look like:

```/pam.+\(sshd:auth\).+failure.+user\=(root|adama)/ ```


### Useful syslog alerts

Here are a couple of alerts you could implement which come in pretty handy:

![screenshot4](http://www.maartenmoerman.nl/screenshots/f2a82a874df5cf9b8d26a39c.png)

### Sending out notifications

To actually send out notifications, you will have to associate the syslog alert rule with the contact. To do this, edit the contact that you have configured and add the syslog rule association:
![screenshot5](http://www.maartenmoerman.nl/screenshots/bcfdb8b29fdc99892a28b24c.png)

Select from the drop down a syslog alert rule, and click "+ Associate". Once you have done this, the association is completed

If you associate it to an email contact, the notification will look like this:

![screenshot5](http://www.maartenmoerman.nl/screenshots/65df301565c86c9614864447.png)
