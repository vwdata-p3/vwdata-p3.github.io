---
title: Demo
layout: default
nav_order: 10
---
## Live demo

We make some remarks on the features of 
the [live demo](/links/demo){:target="_blank"} 
which consists of several panels, 
each one showing a different aspect of the PEP3 system.

### Metering process
The panel marked "**(1)**"
allows one to store a flow record in a our temporary datebase.
Here one takes the role of the "metering process"
that usually generates flow records based
on the traffic through a router.
To store a flow record, 
complete the form,
and press "Submit flow record".
On success
the panel flashes green,
while a red flash indicates an error.
To quickly fill out the form correctly,
press "Randomise all".

### Peers
Before a flow record is stored
its IP addresses are encrypted and pseudonymised.
This operation requires exactly three of the five peers
shown on the "Peers" panel.
A green flash indicates
the involvement of one the peers in some operation.
Accordingly, three peers briefly flash green 
when a flow record that contains a fresh IP address is stored.

By clicking on the circles on can switch the peers on and off,
which is indicated by a green and red border, respectively.
When fewer than three peers are active,
storing a flow record results in an error
(without three active peers, the operation can not be performed.)

Note that aside from you there might be a second visitor
turned peers on and off, and storing flow records.

### Queries
To retrieve flow records one can enter
a query on panel "**(2)**".
There is only one table `peped_flows`,
with 'pseudonymised' fields
`src_ip` and `dst_ip`,
and
 'plain' fields `start_time`, `end_time`, `src_port`, `dst_port`,
`protocol`, `packets`, `bytes`.
One can choose to query the database either as "Researcher"
or as "Investigator", via the select box in the
title of the panel.  Note that the same query results in different
pseudonyms for the researcher and investigator.

Concrete pseudonyms can only be included in a query via a parameter.
To select all flow records with the choosen pseudonymised source IP address,
press "Add parameter", enter `a` under "parameter name", the
choosen pseudonym under "value"[^1], and use the 
following query.
```
SELECT peped_flows.p_src_ip, peped_flows.src_port, 
peped_flows.p_dst_ip, peped_flows.dst_port,
peped_flows.packets FROM peped_flows
WHERE peped_flows.p_src_ip=:a
LIMIT 10
```
If the textbox turns red,
then the query is invalid in some way
(explained in the javascript console).
Note that selecting on a pseudonym of the researcher as the investigator
yields no results.


[^1]: When copying a pseudonym, make sure you copy the whole pseudonym; when the pseudonym contains "+" only one side of it might have been selected.







