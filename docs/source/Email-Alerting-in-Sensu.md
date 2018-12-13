# Email Alerting in Sensu
In this is a tutorial that shows two things:
 * What conditions trigger emails
 * How to reacting to Sensu emails
 * How the email alerting is configured

### Email Triggers
Emails are only sent when some Sensu check on the host is in critical condition. What constitutes as a critical condition is outlined below.

### Network Traffic
When the traffic is over 60 MB/s for over an hour.

### Memory
When the memory on a host is over 90% utilized for over an hour.

### Disk
When the disk is over 90% utilized for over an hour.

### Node down
When a node is down for more than a hour

### Reacting to Sensu emails

**Receiving an mail from Sensu**

You just got an email from Sensu. For this example, the subject is "The CPU Usage is more than 80%" and it's body looks something like this:
```
client name:  compute-39.moc.ne.edu
address:  10.13.37.253
output:  critical
```

This tells you everything you need to know about the event that triggered the email.

**Go to Uchiwa Dashboard**

Click the link to go to the Uchiwa dashboard [http://129.10.5.55:3000/#/login](http://129.10.5.55:3000/#/login). The dashboard will tell you more about the event and has an intuitive UI.

The credentials are as follows:
```
U: admin
P: MOCmonitor2#
```

**Go the client**

Click the second icon down on the navigation bar on the left to see all of the clients. You should see that a check on that client is in critical condition. Click on the client and the click on on the check in critical condition. 

We know that this client is in a critical condition because of the subject line. In this case the CPU Usage is more than 80%, so we need to fix that. Some how you need to make the CPU Usage decrease from 80%.

**Verify the issue has been resolved**
Once you have resolved the issue, you should verify that it has been resolved. You do this by going to the Uchiwa dashboard. 

If the issue has been resolved, you will see the check that was red is now green. 

### Email alerting configuration
An email is triggered after after a client is in a critical condition for an hour. 

This is to avoid sending alerts for clients that reach a critical condition, but do not sustain this critical condition. The emails will continue to send every hour that a client is in a critical condition.

There are three parts to how the email infrastructure is configured, which are a check, handler and filter.

**Check**

Check are what allow you to monitor services or measure resources. Below is an example of how a check is configured.
```
{
  "checks": {
    "cpu_pcnt_metrics": {
      "handlers": ["default", "influxdb-cpu", "idle-email"],
      "type": "metric",
      "command": "/etc/sensu/plugins/cpu-pcnt-usage-metrics.rb -t 20",
      "subscribers": [
        "moc-sensu"
      ],
      "interval": 300,
      "handler": "debug"
    }
  }
}
```

This check measures CPU usage metrics in percentages. It's important to point out that the check runs every 5 minutes. We know this because the interval is 300 seconds.

Also the `-t` flag in to command is for passing in a threshold for the CPU's idle percentage. If a client's CPU idle percentage is less than 20%, that means it's over 80% utilized! 

If this is the case, the check will be in a critical condition for the client. This check uses a handler called idle-email, which will send the email.

If you want to learn more about checks, go to [Sensu's Documentation on checks](https://sensuapp.org/docs/latest/checks)

**Handler**

Handlers allow Sensu to take action on event. Below is an example of how a handler is configured.
```
{
  "handlers": {
    "idle-email": {
      "type": "pipe",
      "filter": "recurrences",
      "severities": [
        "critical"
      ],
      "command": "mailx -s 'The CPU Usage is more than 80%' kaizen@lists.massopen.cloud"
    }
  }
}
```

This handler sends an email when a check is in critical condition. More specifically, this email handler sends and email to kaizen@lists.massopen.cloud when the CPU Usage is more than 80% on a client. This uses mailx which connects to the internet to send the email.

It is important to note that this email uses the filter called recurrences.

If you want to learn more about checks, go to [Sensu's Documentation on handlers](https://sensuapp.org/docs/latest/handlers) 

**Filters**

Filters allow you to filter events for one or more handlers. In this case, without the filter every time a client was in a critical condition, you would receive an email, which could be every 5 minutes. They are the reason why you only receive emails once every hour. 

Below is the email filter.

```
{
  "filters": {
    "recurrences": {
      "attributes": {
        "check": {
          "status": "eval: value == 2"
        },
        "occurrences": "eval: value % 12 == 0"
      }
    }
  }
}
```

We only need one email filter, because it can be applied to one or more handlers. If all of the attributes must be true before a check can trigger an email. If any of the attributes are false, the event is filtered and an email is not sent. 

The occurrences attribute, in this case, is the number of times a check has been in a critical state. Because the checks that can trigger email alerts run every 5 minutes, the check has to be in a critical state for 12 occurrences before an email is sent. 

This means that a check must be in a critical state for at least an hour (12 occurrences * 5 minutes) on a client. As long as a check on a client is in a critical condition, an email will be sent every hour.

If you want to toggle the frequency for sending emails, change the integer 12 to another integer. For example if you want to receive an email 30 minutes after a check on a client is in a critical condition, change the integer 12 to 6.

If you want to learn more about checks, go to Sensu's Documentation on filters [here](https://sensuapp.org/docs/latest/filters)

