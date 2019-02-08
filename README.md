# tacit-dyndns
A dynamic DNS updater for Windows written in C++ that uses tacent and curl. Supports ipv4, ipv6, and updating multiple domains. This tool updates your dynamic DNS provider when your IP changes. The command line allows you to force an update and override the sent ip addresses -- invaluable for determining that you've set everything up correctly.

The tool has been tested with update.spdyn.de but ought to work with other providers -- at worst a small change to the syntax of the curl request would be needed.

The intent is that you run the tool at regular intervals (perhaps every 15 minutes) via a scheduled task. Running the tool does not send an update every time -- the default configuration updates only when a change is detected.

TacitDynDns depends on curl being available. Curl ships with Windows 10 so no need to do anything more on that OS. You may specify an alternate curl executable if you prefer.

Here is an example of a full configuration file for TacitDynDns:

```
<
    Full configuration example for TacitDynDns.

    This config file is in symbolic-expression syntax. This text is in a multiline comment. Any files or external references
    are relative to the working dir unless you specify an absolute path. Use forward slashes for directory separators.
>
[
    environment
    [ statefile     TacitDynDns.ips ]           ; Default. This file stores the last IP addresses successfully sent.
    [ logfile       TacitDynDns.log ]           ; Default. This log is appended to. Tells you when updates were applied/attempted.
    [ verbosity     normal ]                    ; Default. Choices are none, minimal (updates), normal (logs attempts and updates), and full.
    [ iplookup      ifconfig.co ]               ; Default. What external service to use to receive your current IPV4 or IPV6 address. Defaults to ifconfig.co
    [ curl          curl.exe ]                  ; Default. Windows 10 has curl built in. Specify the path to curl.exe if you want to use a different version.
]

[
    update
    [ domain        your.domain.org ]
    [ service       update.spdyn.de/nic/update ]
    [ record        ipv4 ]                      ; Default. Choices are A or AAAA (or use the synonyms ipv4 or ipv6)
    [ protocol      https ]                     ; Default. Choices are http or https. Use https if your service supports it.
    [ username      username ]
    [ password      password ]
    [ mode          changed ]                   ; Default. Choices are always (send every time) or changed (only send if different from last successful update).
]

[
    update
    [ domain        your.domain.org ]
    [ service       update.spdyn.de/nic/update ]
    [ record        ipv6 ]                      ; Choices are A or AAAA (or use the synonyms ipv4 or ipv6)
    [ protocol      https ]                     ; Default. Choices are http or https. Use https if your service supports it.
    [ username      username ]
    [ password      password ]
    [ mode          changed ]                   ; Default. Choices are always (send every time) or changed (only send if different from last successful update).
]
```

You can have as many 'update' blocks as you like. If you want to update the same domain with both an ipv4 and an ipv6, have a block for each.

A minimal config file that updates a single domain's ipv4 address using https looks like this:

```
; Minimal configuration example for TacitDynDns.
[
    update
    [ domain        your.domain.org ]
    [ service       update.spdyn.de/nic/update ]
    [ username      username ]
    [ password      password ]
]
```

The default config file is TacitDynDns.cfg -- or you can specify a different one on the command line. Additional command-line options include the ability to force an update as well as override the IP(s) that get sent.

To see a description of the options run `TacitDynDns --help` in a console.

# build instructions

Open the solution file in VS 2017 and hit build.
