[link_obs_alert_checker]: http://www.observium.org/wiki/Creating_an_Alert_Checker
[link_obs_alerting_metrics]: http://www.observium.org/wiki/Alerting_Metrics_and_Attributes

##Useful Observium Alert Checkers


Observium straight out of the SVN repository doesn't come with alert-checkers, which is 
unfortunate, as you need to figure out how it works by trial and error.

There is some documentation on the Observium site itself, which is useful to read:

*   [Creating_an_Alert_Checker][link_obs_alert_checker]
*   [Alerting_Metrics_and_Attributes][link_obs_alerting_metrics]
 
 
Observium has a very powerful way of using entity types & check conditions to do alerting. 
But you do need to know how this is implemented. 

### Creating an alert checker
Let's go through the steps that are involved to actually create/add an alert checker in Observium

#### Entity type
First of all when you create an alert,you'll need to pick the 'entity' type for what you are building the alert for. An entity type is nothing more than a "thing" for which you would like to see alerts.

These are the ones that are available as of 12/12/2014:

* Device
* Memory
* Storage
* Processor
* BGP Peer
* Netscaler vServer
* Netscaler Service
* Toner
* Port
* Sensor
* Status
 
They kinda speak for them selves, if you want alerts on things that go on with ports, pick
ports, if you want something that has to do with a sensor, pick that one. Device is a very 
generic one, and will just give you status things on wether it's up/down and it's uptime
and the response time for ping/snmp & uptime, the entity type Device has nothing to do with
Ports or Sensor on the device itself, for alerting for that, pick actually Ports or Sensor

#### Alert Checker details

Once you picked the entity type, there's a couple of more things that need to be filled in
but these are simple, pick a name for the alert, and pick a message you want to be included 
once an alert is sent out.

Use Alert Delay to set the amount of poller runs that a condition of your alert checker should
persist until it actually starts alerting. This could be useful when for example you're 
creating a check for processor usage, but you don't want to be alerted on every CPU spike 
that is happening. If you set a delay of say, 2, it'll take 2 poller runs for actually 
alerting (providing the condition for which you are checking hasn't changed off course)

Send Recovery button is self explanatory, and the Severity is currently not in use

#### Checker Conditions 

Then we come to the Checker Conditions, this is where you actually implement the check for a specific entity.

It's import to know what Metrics & Attributes are, see the overview below for a complete list of Metrics & Attributes

When filling in the fields for Checker Conditions, you use the Metrics mentioned in this page.

These need to be single lined entries, you can put as much in there if you want but you usually have one to check for a single condition, or two, for example to check an upper and lower limit. Use the boolean to switch between ANY or ALL of these conditions to match.

A single line consists of three values:

* the actual metric 
* a "test" (le, ge, lt, gt, ne, match and notmatch)
* a value

#### Associations

In these input fields you’ll create the first association rule, in other words, which subset of the entity type you selected needs alerting based on the conditions specified in the previous pane. When initially creating an alert checker, it allows for ony 1 association rule. Once it’s added, you can later on add more association rules to it.

These association rules are made from a “device association” and an “entity association”. First input field you’ll do your device matching, based on the attributes for devices. Second input field you’ll do your entity matching, using the attributes for the entity type you want to associate it with (this can off course be different then the condition you’re checking for)

This works in sort of the same way as the Checker Conditions. It uses the same line method (metric,test,value), however with some exceptions:

*  instead of using metrics, you’ll be using attributes
*  you can’t use a device attribute twice in the same association rule, so for example multiple “hostname match bla” statements with in the same association rule won’t work. You will need to add multiple association rules.
*  for a single device association line, you can have multiple entity association lines

That last exception allows for more specific filtering, for example, you would want to match against all sensor classes (sensor_class) that are of type “state”, but when that nets you to many results, you can add a match for it’s description (sensor_descr), or you’d want to match all ports of type (ifType) ethernetCsmacd, but you only want certain ones with a specific description (ifAlias).



### Example alerts

<font color='red'>If you scrolled down here to just copy/paste some alert-checkers, perfectly fine, but don’t complain if they don’t work, PLEASE read how these work above.</font>

The following is a set of very useful alert checkers:

| Alert                                   | Entity type | Check Conditions                                                       | Check Conditions boolean | Device match | Entity match                           |
|-----------------------------------------|-------------|------------------------------------------------------------------------|--------------------------|-------------:|----------------------------------------|
| Device down                             | Device      | device_status equals 0                                                 | ANY                      |            * | *                                      |
| Processor usage is above 80%            | Processor   | processor_usage greater 80                                             | ALL                      |            * | processor_descr match *processor*      |
| Memory usage is above 70%               | Memory      | mempool_perc greater 70                                                | ALL                      |            * | *                                      |
| State sensor is in ALERT state!         | Status      | status_event = alert status_event = warning                                             | ANY                      | *            |*              |
| Fanspeed is above or under treshold     | Sensor      | sensor_value greater @sensor_limit sensor_value less @sensor_limit_low | ANY                      | *            | sensor_class equals fanspeed           |
| Traffic exceeds 85%                     | Port        | ifInOctets_perc ge 85 ifOutOctets_perc ge 85                           | ANY                      | *            | ifType equals ethernetCsmacd           |
| BGP Session down                        | BGP Peer    | bgpPeerState notequals established                                     | ANY                      | *            | bgpPeerRemoteAs equals 41552           |
| Storage exceeds 85% of disk capacity    | Storage     | storage_perc ge 85                                                     | ANY                      | *            | storage_type equals hrStorageFixedDisk |
| Port has encountered errors or discards | Port        | ifInErrors_rate gt 1 ifOutErrors_rate gt 1                             | ANY                      | *            | ifType equals ethernetCsmacd           |
| Port is enabled, but operationally down | Port        | ifAdminStatus equals up ifOperStatus notequals up                      | ALL                      | *            | ifType equals ethernetCsmacd           |



###Per entity overview of Attributes , Metrics and their values (if any)

#### Device
<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>device_status</td><td>0 = down, 1 = up</td></tr>
    <tr><td>device_status_type</td><td>reason for down, 'snmp'/'ping'</td></tr>
    <tr><td>device_ping</td><td>response in ms</td></tr>
    <tr><td>device_snmp</td><td>response in ms</td></tr>
    <tr><td>device_uptime</td><td>in seconds</td></tr>
    <tr><td>device_duration_poll</td><td>in seconds</td></tr>
   
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>hostname</td><td>Self explanatory, this is the hostname for the device</td></tr>
    <tr><td>os</td><td>cisco,asa,junos,linux,printer, generic, etc.
For an up-to-date list see /opt/observium/includes/definitions/os.inc.php</td></tr>
    <tr><td>type</td><td>network,server,workstation,storage,voip,firewall</td></tr>
    <tr><td>sysName</td><td>Derived through SNMP</td></tr>
    <tr><td>sysDescr</td><td>Derived through SNMP</td></tr>
    <tr><td>sysContact</td><td>Derived through SNMP</td></tr>
    <tr><td>hardware</td><td>Derived through SNMP</td></tr>
    <tr><td>serial</td><td>Derived through SNMP</td></tr>
</table>

#### Port
<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>ifInOctets_rate & ifOutOctets_rate</td><td>number</td></tr>
    <tr><td>ifInOctets_perc & ifOutOctets_perc</td><td>0-100 percentage</td></tr>
    <tr><td>ifInUcastPkts_rate & ifOutUcastPkts_rate</td><td>number</td></tr>
    <tr><td>ifInErrors_rate & ifOutErrors_rate</td><td>number</td></tr>
    <tr><td>rx_ave_pktsize & tx_ave_pktsize</td><td></td></tr>
    <tr><td>ifOperStatus</td><td>up/down</td></tr>
    <tr><td>ifAdminStatus</td><td>up/down</td></tr>
    <tr><td>ifSpeed</td><td>interface speed derived through SNMP in mbit</td></tr>
    <tr><td>ifMtu</td><td>number</td></tr>
    <tr><td>ifDuplex</td><td>full/half</td></tr>
   
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>ifSpeed</td><td>interface speed in a mbit number</td></tr>
    <tr><td>ifAlias</td><td>the interface description</td></tr>
    <tr><td>ifDescr</td><td>Location of the interface, (blade, slot, etc)</td></tr>
    <tr><td>ifName</td><td></td></tr>
    <tr><td>ifType</td><td>name of interface as described by IANA, see https://www.iana.org/assignments/ianaiftype-mib/ianaiftype-mib</td></tr>
    <tr><td>ifPhyAddress</td><td>MAC address of the interface</td></tr>
    <tr><td>port_descr_type</td><td></td></tr>
    <tr><td>port_descr_descr</td><td></td></tr>
    <tr><td>port_descr_speed</td><td></td></tr>
    <tr><td>port_descr_circuit</td><td></td></tr>
    <tr><td>port_descr_notes</td><td></td></tr>
</table>

#### Memory
<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>mempool_free</td><td></td></tr>
    <tr><td>mempool_perc</td><td>0-100 percentage</td></tr>
    <tr><td>mempool_used</td><td></td></tr>
   
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>mempool_descr</td><td></td></tr>
    <tr><td>mempool_mib</td><td></td></tr>
    <tr><td>mempool_index</td><td></td></tr>
</table>

#### Processor

<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>processor_usage</td><td>0-100 percentage</td></tr>
   
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>processor_descr</td><td></td></tr>
    <tr><td>processor_type</td><td></td></tr>
    <tr><td>processor_oid</td><td></td></tr>
</table>


####Storage

<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>storage_free</td><td></td></tr>
    <tr><td>storage_perc</td><td>0-100 percentage</td></tr>
    <tr><td>storage_used</td><td></td></tr>
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>storage_descr</td><td></td></tr>
    <tr><td>storage_type</td><td></td></tr>
    <tr><td>storage_mib</td><td></td></tr>
    <tr><td>storage_index</td><td></td></tr>
</table>

####BGP Peer

<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>bgpPeerState</td><td>idle, connect, active, opensent, openconfirm, established</td></tr>
    <tr><td>bgpPeerAdminStatus</td><td>stop, start</td></tr>
    <tr><td>bgpPeerFsmEstablishedTime</td><td>returns seconds the peer is live</td></tr>
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>as_text</td><td></td></tr>
    <tr><td>bgpPeerRemoteAs</td><td>AS number of the remote peer</td></tr>
    <tr><td>bgpPeerRemoteAddr</td><td>IP address of the remote peer</td></tr>
    <tr><td>bgpPeerLocalAddr</td><td></td></tr>
    <tr><td>bgpPeerIdentifier</td><td></td></tr>
</table>

#### Sensor


<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>sensor_value</td><td>number</td></tr>
    <tr><td>sensor_event</td><td>Status of the sensor compared to the thresholds configured on the sensor</td></tr>
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>sensor_descr</td><td></td></tr>
    <tr><td>sensor_class</td><td>voltage, current, power, frequency, humidity, fanspeed, temperature, dbm, state</td></tr>
    <tr><td>sensor_type</td><td></td></tr>
    <tr><td>sensor_index</td><td></td></tr>
    <tr><td>poller_type</td><td>possible types: snmp, agent, ipmi</td></tr>
</table>

#### Status

<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>status_value</td><td>number</td></tr>
    <tr><td>status_event</td><td>up, warning, alert, down</td></tr>
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>status_descr</td><td>Description</td></tr>
    <tr><td>status_type</td><td>Device & code specific - only for custom things</td></tr>
    <tr><td>status_index</td><td>just index in the table, not really useful for alerting</td></tr>
    <tr><td>status_oid</td><td>SNMP OID - basically don't use</td></tr>
</table>

#### Toner
<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>toner_current</td><td></td></tr>
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>toner_descr</td><td></td></tr>
</table>

####Netscaler vServers

<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>vsvr_state</td><td>up, down, outOfService</td></tr>
    <tr><td>vsvr_bps_in</td><td>bits/sec in</td></tr>
    <tr><td>vsvr_bps_out</td><td>bits/sec out</td></tr>
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>vsvr_name</td><td>Name of the vserver - this matches vsvr_fullname except when longer then 32chars, it becomes a randomstring</td></tr>
    <tr><td>vsvr_fullname</td><td>Name of the vserver</td></tr>
    <tr><td>vsvr_label</td><td></td></tr>
    <tr><td>vsvr_ip</td><td></td></tr>
    <tr><td>vsvr_ipv6</td><td></td></tr>
    <tr><td>vsvr_port</td><td></td></tr>
    <tr><td>vsvr_type</td><td></td></tr>
    <tr><td>vsvr_entitytype</td><td></td></tr>
</table>

####Netscaler Services

<table>
	<tr><th>Metrics</th><th>Values</th></tr>
    <tr><td>svc_state</td><td>up, down, outOfService</td></tr>
    <tr><td>svc_bps_in</td><td>bits/sec in</td></tr>
    <tr><td>svc_bps_out</td><td>bits/sec out</td></tr>
	<tr><th>Attributes</th><th>Values</th></tr>
    <tr><td>svc_name</td><td>Name of the service - this matches vsvr_fullname except when longer then 32chars, it becomes a randomstring</td></tr>
    <tr><td>svc_fullname</td><td>Name of the service</td></tr>
    <tr><td>svc_label</td><td></td></tr>
    <tr><td>svc_ip</td><td></td></tr>
    <tr><td>svc_port</td><td></td></tr>
    <tr><td>svc_type</td><td></td></tr>
</table>






