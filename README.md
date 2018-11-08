# hashms

hashms runs during a hashcat cracking session and checks the given outfile (-o/--outfile parameter in hashcat) at specified intervals. If the outfile has additional lines (i.e. additional hashes have been cracked) hashms sends a notification via SMS and/or Slack. The intent is to reduce the delay between cracking a hash and follow-on operations, as well as the manual effort involved in checking and re-checking ongoing cracking sessions.

hashms uses [Textbelt](https://textbelt.com/) for SMS. An API key is required for SMS, and a [Slack webhook URL](https://api.slack.com/incoming-webhooks) is required for Slack messages. 

## Installation

Clone the repositoriy and install the requirements:

    git clone https://github.com/WJDigby/hashms.git
    pip3 install -r requirements.txt
    
The only non-standard library required is [requests](http://docs.python-requests.org/en/master/). The repository includes an example configuration file.

## Use

Run hashms in a screen, tmux, or other terminal session while hashcat is running and provide it the name of your hashcat outfile to monitor. If you are running on a shared machine, or the user running hashms is different than the user running hashcat, make sure hashms has permissions to read the outfile. The hashcat64 process name is hardcoded in hashms.py. 

The Textbelt API and SLack URL values can be set with environmental variables or with a configuration file. Running hashms with command-line parameters -p / --phone-number and/or -s / --slack will look for environmental variables.

    python3 hashms.py -o hashes.outfile -p 5551234567
    
Running hashms.py with the configuration file option (-c / --config) will cause it to look for the Textbelt API key, phone number, Slack URL, and Slack username in the configuration file. Configuration files may be useful in situations where a team shares a cracking rig.

Arguments include:
* -o / --outfile - The location of the hashcat outfile to monitor (mandatory)
* -i / --interval - Interval in minutes between checks. Default is 15. This is a float value in case the operator wants to check more often than once a minute.
* -n / --notification-count - How many notifications to send overall. Default is 5. This is useful in case the operator is running a large cracking job and does not want to eat through all of his or her Textbelt quota. 
* -t / --test - Send a test message to the configured options. Does not count against the notification count, but an SMS will of course count against the Textbelt quota.
* -c / --config - Use a configuration file. A sample configuration file is included in the repo. If used, hashms expects all notification values to come from the configuration file. This option is mutually exclusive with -p and -s.
* -p / --phone-number - Phone number to send SMS to. Textbelt API will come from environmental variable.
* -s / --slack - If enabled, send a slack message. Slack URL will come from environmental variable.
* -u / --user - Slack user to send the message to (e.g. @user). Useful to get targeted slack notifications.

## Examples

Check the file hashes.out hourly. If additional hashes have been cracked, send a text to (555) 123-4567 and a slack message to the user wjdigby. Send a maximum of 10 notifications:

    python3 hashms.py -o hashes.out -i 60 -n 10 -p 5551234567 -s -u wjdigby
    
Check the file hashes.out every 10 minutes. If additional hashes have been cracked, send a Slack message based on the contents of a configuration file (to send only Slack messages when using a configuration file, only fill out the Slack options. Likewise for SMS).

    python3 hashms.oy -o hashes.out -i 10 -c hashms.conf



