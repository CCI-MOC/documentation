For PEP8 tools are your friends.  There are several editors that support PEP8 natively, however I am going to mention command line tools.

1) flake8 - static python code analyzer
2) autopep8 - pretty printer (can automatically fix most of what flak8 complains about)
3) yapf - (yet another python formatter), this may do a better job than autopep8.

There is a project wide use of flak8/autopep8.  To make it easier to review group all of the similar errors/warnings together in a series of commits.

