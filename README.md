# dyndns46
A dynamic DNS updater for Windows written in C++ that uses the tacent modules and curl. Supports ipv4, ipv6, and updating multiple domains. This tool updates your dynamic DNS provider when your IP changes. The command line allows you to force an update and override the sent ip addresses -- invaluable for determining that you've set everything up correctly.

The tool has been tested with update.spdyn.de but ought to work with other providers -- at worst a small change to the syntax of the curl request would be needed.

The intent is that you run the tool at regular intervals (perhaps every 15 minutes) via a scheduled task. Running the tool does not send an update every time -- the default configuration updates only when a change is detected.

TacitDynDns depends on curl being available. Curl ships with Windows 10 so no need to do anything more on that OS. You may specify an alternate curl executable if you prefer.

Here is an example of a full configuration file for DynDns46:

```
<
    Full configuration example for DynDns46.

    This config file is in symbolic-expression syntax. This text is in a multiline comment. Any files or external references
    are relative to the working dir unless you specify an absolute path. Use forward slashes for directory separators.
>
[
    environment
    [ statefile     TacitDynDns.ips ]           ; Default. This file stores the last IP addresses successfully sent.
    [ logfile       TacitDynDns.log ]           ; Default. This log is appended to. Tells you when updates were applied/attempted.
    [ verbosity     concise ]                   ; Default. Choices are none, concise (one line per day), minimal (updates only), normal (logs attempts and updates), and full.
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
; Minimal configuration example for DynDns46.
[
    update
    [ domain        your.domain.org ]
    [ service       update.spdyn.de/nic/update ]
    [ username      username ]
    [ password      password ]
]
```

The default config file is DynDns46.cfg -- or you can specify a different one on the command line. Additional command-line options include the ability to force an update as well as override the IP(s) that get sent.

The various logging verbosity settings are as follows:

* none : No log file generated.
* concise : One log file line per day. A '-' means the script was run but no updates were sent. A '4' means at least one ipv4 block updated. A '6' means at least one ipv6 block was updated. An 'A' means at least one ipv4 block AND at least one ipv6 block was updated. In this mode if you had set the scheduled task to run every 15 minutes, you'd have (24*60)/15 = 96 characters per log line, with one line generated per day.
* minimal : Only updates logged.
* normal : Includes minimal as well as logging logging when no update was required.
* full : Includes normal in addition to details like printing the cURL commands that were issued.

To see a description of the options run `DynDns46 --help` in a console.

# build instructions

Open the solution file in VS 2019 and hit build.
