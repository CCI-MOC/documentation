## Defination

Logstash filter plugins are used to perform intermediary processing on Logs. In our production, we use ```grok``` filter to extract various information from different service logs and add tags to them. The reason why we use filters is that it will be very helpful for us to do search and data visualization in Kibana.

## What is grok filter

There are many filter plugins that already developed for you to use. You could find them [Here](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html)

For our production Logstash, we use grok filter to parse logs collected by Filebeat. Grok works by combining text patterns into something that matches your logs. The syntax for a grok pattern is ```%{SYNTAX:SEMANTIC}```.

```Text patterns``` are defined by regular expressions. You could find many predefined patterns [here](https://github.com/elastic/logstash/blob/v1.4.2/patterns/grok-patterns). If you want to build your own patterns, you will find [this one](http://grokdebug.herokuapp.com) and [this one](http://grokconstructor.appspot.com/) applications quite useful.

The ```SYNTAX``` is the name of the pattern that will match your text. For example, ```3.44``` will be matched by the ```NUMBER``` pattern and ```55.3.244.1``` will be matched by the ```IP``` pattern. The syntax is how you match.

The ```SEMANTIC``` is the identifier you give to the piece of text being matched. For example, ```3.44``` could be the duration of an event, so you could call it simply ```duratio```n. Further, a string ```55.3.244.1``` might identify the ```client``` making a request.

For the above example, your grok filter would look something like this:

```%{NUMBER:duration} %{IP:client}```

## Production Logstash filter

The Production logstash filter is configured like below:
```
filter {
    if [type] == "neutron" or [type] == "horizon" or [type] == "nova" or [type] == "glance" or [type] == "cinder" or [type] == "keystone" or [type] == "ceilometer" or [type] == "heat" {
        grok {
            patterns_dir => ["/opt/logstash/patterns"]
            match => { 'message' => '%{STACKPATTERN}'}
        }
    }

    if [type] == "horizon" and [module] == "openstack_auth.forms" {
        grok {
           match => { 'logmessage' => '%{QUOTEDSTRING:username}'}
        }
    }
}
```

When Filebeat collectting logs it will add 'type' field to the log according to what openstack service the log message comes from. Such as 'nova', 'glance', 'horizon'... This is for use in Logstash. When Logstash received these logs from Filebeat, it will parse them using (maybe) different patterns according to service types. The grok patterns are defined in ```/opt/logstash/patterns```. The production patterns is shown below:
```
MODULE \b(?:[0-9A-Za-z_][0-9A-Za-z-_]{0,62})(?:\.(?:[0-9A-Za-z_][0-9A-Za-z-_]{0,62}))*(\.?|\b)
STACKPATTERN %{TIMESTAMP_ISO8601:date} %{BASE10NUM:processID} %{LOGLEVEL:loglevel} %{MODULE:module} ?(\[(%{NOTSPACE:request})?\s?(%{NOTSPACE:user})?\s?(%{NOTSPACE:tenant})?\s?(%{NOTSPACE:domain})?\s?(%{NOTSPACE:user_domain})?\s?(%{NOTSPACE:project_domain})?\])? %{GREEDYDATA:logmessage}
CEPHPATTERN %{TIMESTAMP_ISO8601:date} %{GREEDYDATA:logmessage}
```
As you could see above, we could extract many informations like datetime, processID, loglevel, module... from log messages. It is used for future search and visualization in Kibana.