# SNMP-prober
A script to probe an SNMP agent and find the rate of change for several counters between successive probes or samples. The rate calculated for each counter/OID is displayed on the console, a line is dedicated for each calculated rate, the output format is described in detail in 'output format'. This script can handle both 32 and 64 bit versions of counters and in the case that a counter wraps (ie goes from a high number to a low number) this solution addresses and rectifes it. The solution also handles an SNMP agent restart (i.e. the sysUpTime OID becomes less than it was before, ie. it starts counting from zero), and timeouts, i.e. the device does not respond to your request in time.  

The script in invoked as follows:

prober <Agent IP:port:community> <sample frequency> <samples> <OID1> <OID2> …….. <OIDn>

where,

IP, port and community are agent details,

OIDn are the OIDs to be probed (they are absolute, cf. IF-MIB::ifInOctets.2 for interface 2, or 1.3.6.1.2.1.2.2.1.10.2 [1]) 

Sample frequency  (Fs) is the sampling frequency expressed in Hz, you should handle between 10 and 0.1 Hz. 

Samples (N) is the number of successful samples the solution should do before terminating, hence the value should be greater or equal to 2. If the value is -1 that means run forever (until CTRL-C is pressed, or the app is terminated in someway). 

 
Output format is

Sample time | OID1 | OID2 | .... | OIDn

where, 
Sample time: Timestamp of the last sample, in UNIX time (seconds). 

OID*: Rate of OID* between the last two successful samples

 

As an example:

1504083911  | 2124 | 819 | 0 | 281761 
1504083912  | 2471 | 819 | 110 | 450782 
1504083913  | 1904 | 819 | 2000 | 325448 
 

--Technical Testing--: 

This solution is tested against a simulated/real SNMP agent, where the behavior is known.

https://github.com/patrikarlos/anm, A2 is used for the evaluation. 

 

A test script will launch the solution and validate the results against the known results. The script will check:

- that the solution generates the correct number of samples, as requested, at the requested sample frequency. 

- that the solution sends the snmp request at the required sampling frequency, will be tested by packet tracing. 

- that the SNMP request contain all the requested OIDs, by packet tracing

- that the solution handles non-responsive SNMP agents, ie. timeouts. Ie the inter sample time presented in console would be n/Fs where Fs is sample frequency and n=1,2,3,... 

- that the solution handles that the SNMP agent restarts/reboots

- that the output from the solution matches the configuration of the agent

- that the solution handles counters that wrap around, both 32 and 64 bit counters. 
