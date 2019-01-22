### How to Read the Logs

If you don't already have public key access to moc02, send a request to moc-ops-team-list@bu.edu and attach your public key.

    $ ssh <username>@moc02.bu.edu 
    $ ssh moc@logger-bot
 
When prompted for a password, type `MOC-logs.4`.  There is a short README file in the `/home/moc` directory.

In `/home/moc/logs_#moc/` there are copies of the channel logfiles. They are synced with the originals every 10 minutes, so if you are looking for very recent activity, you might have to wait a few minutes.


### Technical details

* [Documentation for the Limnoria bot is here](http://doc.supybot.aperio.fr/en/latest/)
* IP address (from moc02): `192.168.122.239`
* owner users of the bot are currently kamfonik, gsilvis, and jayh.

Please contact moc-ops-team-list@bu.edu with questions or concerns about MOC-bot.


