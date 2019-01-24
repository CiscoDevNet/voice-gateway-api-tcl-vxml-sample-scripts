TCL script for enhanced CER scalability

Currently JTAPI does not allow the application to change the calling number when doing a JTAPI Redirect. This can create scalability issues for Cisco Emergency Responder when a large number of ERL’s are required. Each ERL requires a separate route point on CCM so the calling party number can be transformed according to the ELIN for that ERL.

This note briefly describes an IOS TCL scripts that allows the calling party transform to take place on the H323 gateway connecting to the PSTN. CER is then configured so that the ELIN is appended to the called number before the call is sent back to CCM. When received by CCM the format of the called number might be NN911EEEEEEEEEE, where NN is a gateway selector and EEEEEEEEEE is a 10 digit ELIN. So on CCM only one route pattern is required for each PSTN gateway. I.e. for the first gateway you may configure the route pattern 10911XXXXXXXXXX, and for the second gateway you might configure the route pattern 20911XXXXXXXXXX. So one route pattern can service all CER calls going through the same gateway can be serviced by a single route pattern. 

When a call reaches a GW it is routed to the CER TCL script by a dial peer that matches on the NN911 prefix of the called number. This TCL script then gleans the ELIN of the called number and makes it the new calling number. At the same time the script replaces the called number with a configurable value (typically 911 or 9911). The call is then passed of to the PSTN.

The scripts have been developed and tested using IOS 12.2(15)T but should work with any 12.2 or 12.2T version. The script works with H.323 only, it does not work with MGCP.

To install and configure the scripts perform the following steps:

1)	Upload the scripts to a TFTP server.

2)	On the H.323 IOS gateway add the following configuration:

call application voice cer tftp://10.1.1.1/cer.tcl
call application voice cer prefix <prefix>
call application voice cer emergency <number>
 
The parameters prefix is the prefix that must be removed from the called number in order to be left only with the ELIN. I.e. if the format of the called number received from CER is 10911EEEEEEEEEE then prefix would be configured as 10911.

The parameters emergency is the value that the called number will be changed to. Typically you’ll want to set this to the emergency number, i.e. 911. Keep in mind that the call will eventually be routed to the PSTN via the standard IOS POTS dial peers, so if these dial peers assume that the PSTN access code is present in the called number then that access code should also be added to emergency. For example if the POTS dial peers route calls to the PSTN based on ‘destination-pattern 9’then emergency should be set to 9911. The leading 9 is then stripped by the outbound POTS dial peer.

3)	Add a voip dial-peer that routes all CER calls to the PSTN through the cer.tcl script. I.e. if calls from CER have the format 10911EEEEEEEEEE then add this dial-peer

dial-peer voice 1 pots
 application cer
 incoming called-number 10911..........

Useful troubleshooting commands on the gateways include:

-	debug voip ivr script
-	debug voip ivr all
-	debug isdn q931

