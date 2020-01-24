---
title: Demo
layout: default
nav_order: 10
---
## Live demo

We make some remarks on the features of the demo
which consists of several panels, 
each one showing a different aspect of the PEP3 system.

[Go to demo](/links/demo){: .btn .btn-blue}{: target="_blank" }

### Metering process
The panel marked "**(1)**"
allows one to store a flow record in a our temporary database.
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

By clicking on the circles one can switch the peers on and off,
which is indicated by a green and grey border, respectively.
When fewer than three peers are active,
storing a flow record results in an error.
(Without three active peers, the operation can not be performed.)

When peers are flashing and changing without your intervention,
this is probably caused
by another visitor of the webdemo.
You may also find their records in the database.
To limit interference,
we empty the database and reset the peers
every 10 minutes.



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
To select all flow records with the chosen pseudonymised source IP address,
press "Add parameter", enter `a` under "parameter name", the
chosen pseudonym under "value"[^1], and use the 
following query.
```
SELECT peped_flows.p_src_ip, peped_flows.src_port, 
peped_flows.p_dst_ip, peped_flows.dst_port,
peped_flows.packets FROM peped_flows
WHERE peped_flows.p_src_ip=:a
LIMIT 10
```
If the text box turns red,
then the query is invalid in some way
(explained in detail in the javascript console).
Note that selecting on a pseudonym of the researcher as the investigator
yields no results.

[^1]: When copying a pseudonym, make sure you copy the whole pseudonym; when the pseudonym contains "+" only one side of it might have been selected by double clicking.

In order to keep the pseudonyms the database
uses internally confidential
not every SQL query is allowed.
Obviously,
direct comparisons between pseudonyms and 'plain' values such as
```
SELECT peped_flows.p_src_ip FROM peped_flows
WHERE peped_flows.p_src_ip="some longer string"
```
are banned, and neither is an inequality test of the form
```
SELECT peped_flows.p_src_ip FROM peped_flows
WHERE peped_flows.p_src_ip<"e"
```
allowed, as it would reveal the values of these pseudonyms by bisection.
Less revealing operations such as
ordering by a pseudonymised column, 
```
SELECT peped_flows.p_src_ip FROM peped_flows
ORDER BY peped_flows.p_src_ip
```
are disallowed as well,
on the general principle that the ordering
of the database's pseudonyms is none of the querier's concern.
In the same vein, 
operations such as `+`, `-`, `SUM`, ...
on pseudonyms
are rejected too. 

Counting the number of pseudonyms, 
on the other hand,
is perfectly acceptable:
```
SELECT COUNT(peped_flows.p_src_ip), peped_flows.protocol  FROM peped_flows 
GROUP BY peped_flows.protocol
```
For this demonstration,
we analysed and support
[only a small subset of SQL](https://github.com/vwdata-p3/webdemo/blob/master/resources/sql.grammar);
the additional restrictions placed on these queries
for dealing with pseudonyms
are set out [here](https://github.com/vwdata-p3/webdemo/blob/master/sql.py).

### Depseudonymisation

In certain circumstances depseudonymisation
of an pseudonymised IP address might be appropriate,
for example, when a pseudonymised IP address
appears to
commands and controls a botnet.
Peers will not comply with such a request
unless a 'warrant' to this effect
is presented
that is signed by
a separate designated entity.
How they should come to their decision to issue the warrant
 is beyond the scope of 
this demonstration,
but you can take their role
via panel "**(3)**" marked "Judge".
Just copy a pseudonym you retrieved via panel (2)
into the text box, select whose ("Researcher" or "Investigator") 
pseudonym is being
depseudonymised, and press "Sign"
to create the warrant.

Note that the warrant mentions the pseudonym
only in encrypted form,
so that the warrant can be presented to the peers 
without revealing the pseudonym.

To perform the depseudonymisation,
copy[^1] the encrypted pseudonym, and signature,
from panel "(3)" to panel "**(4)**", 
select your role ("Researcher" or "Investigator"),
and hit "Submit request". 
(Be sure the database was not cleared in the meantime.)

Note that the "Investigator" cannot present a warrant in panel "(3)"
that is issued to the "Researcher";
the peers will reject it.


Since to whom a pseudonym belongs
a judge cannot tell it is in principle possible
to create
a warrant that allows the investigator to depseudonymise 
one of the researcher's pseudonyms.
But upon execution of the warrant
the peers will interpret the encrypted pseudonym
as one of the investigator's,
and the depseudonymisation
will
result in failure or garbage.

### Notes
{: .text-delta }

