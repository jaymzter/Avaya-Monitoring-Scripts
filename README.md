# Avaya-Monitoring-Scripts
A growing set of scripts used to monitor Avaya PBXs

# Scripts in this repository
## HP NA
The scripts in this folder were originally built to run from a [HP Network Automation](https://en.wikipedia.org/wiki/HP_Network_Management_Center#HP_Network_Automation_Software) NMS. They have been modified so that you only need to change the `hpna` flag within the scripts from `0` to `1` to run them from the shell instead of HP NA. Be aware that for simplicities sake, when run from a shell the scripts expect a password to be given as an argument, which is undesirable from a security standpoint. It is however trivial to modify the scripts to ask for a password instead, e.g.

        send_user "\nPlease enter your password: "
        stty -echo
        expect_user -re ".*\n"
        set pass $expect_out(0,string)

All of the scripts perform the following general functions:
1. Automatically connect and log in to the PBX using SSH.
2. Once logged in, run OSSI scripts straight from the SAT terminal.
3. Output is formatted as CSV for use in your pretty Excel spreadsheets.

### avaya-device-list
This script performs the following actions:
* Log into the PBX and perform a `list station` command, collecting the extensions, station name, and station type.
* Using the information from the previous command, perform a `status station XXX` to retrieve the following when available: firmware version, IP address, MAC address, and registration status.
* This script was tested against a system with around 6200 stations, so if you have more and the script isn't capturing all the stations, you may need to modify the `matchmax` or `timeout`settings in the script.

This script will take a while to run as we are statusing every station in the system. The reason we do this is because we want to know how many TDM stations we have as we move entirely to VoIP, and also to correlate with our licensing pull.

## avaya-phone-licenses
This script peforms a `display capacity` command to pull a subset of license values from the PBX. Values shown are "Used" and "System Limit" of what the PBX is licensed for. This script is self-documenting in that the values pulled are created as an array holding the fields we're pulling from the system.

## avaya-trunk-grps
What this script does:
* Log in to the PBX and run `list trunk`
* Collect the trunk group number, trunk group name, trunk group type, and total channels.
* Next, status each trunk group number, collecting board number, channel number, and current service state

Depending on how many trunks the system has, it may also take a while to run.

## Nagios
These scripts run as a service under Nagios, and will execute arbitary OSSI commands on the target PBX.
