##How to configure 1 minute polling on Observium


1 Minute polling for Observium, quite an interesting and useful feature if you ask me. Especially with large capacity interfaces such as 25/40/100gbit interfaces, there’s tiny differences which could be interesting to spot, and you just can’t see those when you are using 5 minute polling. Another interesting used case for example are your uplinks, and your core links in your network.

By default Observium is not setup to do this, and it requires a couple of changes. Now luckily, it’s not that hard to change this, as the configuration settings for the RRDs are a bit hidden, but setup in such a way, that you can actually change this.

First before we get into the details on how to actually do this there are a couple of things you’ll need/want to know:

#####<span style="color:red">N E E D&nbsp;&nbsp;&nbsp;T O&nbsp;&nbsp;&nbsp;K N O W :</span>

* These changes are an all or nothing setup. Meaning, all your things will be polled on a 1 minute interval, that is sensors, ports, cpu’s, memory, everything. No possibility to do just a couple of things on 1 minute intervals
* You NEED to do this with an empty RRD directory / new installation, as RRD files need to be recreated with the proper ‘time brackets’ configured in them
* I have not tested it on an existing installation, so i don’t know what will happen, you could probably loose your data, or at least corrupt it. I simply don’t know enough of RRDtool what will happen.
* Make sure all your devices finish their polling within 1 minute
* It’s NOT, and i repeat NOT an officially supported thing. Observium (Adam Armstrong) recommends against it
* There will be a 5time increase in load on your server
* Your disk IO will increase by 5 times as well. Keep that in mind if your are for example runnning your RRD directory on an SSD, as this will wear out 5 times as fast



####Configuration
Ok, let’s see how to configure this:

In the default configuration thats deployed once you have downloaded it, the RRD settings are configured for:

* 7 days of 5 minutes
* 62 days of 30 minutes
* 120 days of 2 hours
* 4 years of 1 day

Now these settings are perfectly acceptable for me, except the first one ofcourse. We’ll want that to change to 1 minute.

You’ll need to make changes in 2 places:

* Observium configuration file
* Crontab

Your configuration is stored in, and you can lookup them up in the file **/opt/observium/includes/defaults.inc.php**

It looks like this:


	// Default Poller Interval (in seconds)
	$config['rrd']['step'] = 300;
	// 7 days of 5 min 62 days of 30 min 120 days of 2 hour 4 years of 1 day
	$config['rrd']['rra'] = "RRA:AVERAGE:0.5:1:2016 RRA:AVERAGE:0.5:6:2976 RRA:AVERAGE:0.5:24:1440 RRA:AVERAGE:0.5:288:1440 ";
	$config['rrd']['rra'] .= " RRA:MIN:0.5:6:1440 RRA:MIN:0.5:96:360 RRA:MIN:0.5:288:1440 ";
	$config['rrd']['rra'] .= " RRA:MAX:0.5:6:1440 RRA:MAX:0.5:96:360 RRA:MAX:0.5:288:1440 ";
	
Now you could modify the RRD settings in this file, but that’s not really helping if the default settings get adjusted when you upgrade your Observium instance. So change it in the file **/opt/observium/config.php**

Change 2 things in that piece of text:

* the rrd step size from 300 to 60
* the first RRA value from RRA:AVERAGE:0.5:1:2016 to RRA:AVERAGE:0.5:1:10080

Once this has been changed, change your cronjob (**/etc/cron.d/observium**) to run poller-wrapper / poller every minute

That should be it! Enjoy your 1 minute resolution in Observium!





