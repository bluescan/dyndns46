# tacit-dyndns
A dynamic DNS updater for Windows written in C++ that uses tacent and curl. Supports ipv4, ipv6, and updating multiple domains. This tool updates your dynamic DNS provider when your IP changes. The command line allows you to force an update and override the sent ip addresses -- invaluable for determining that you've set everything up correctly.

_Although the tool is working, it has only been tested with spdyn.de. It also does not process the command line options yet._

The tool has been tested with update.spdyn.de, but ought to work with other providers (possibly with minimal changes).

The intent is that you run the tool at regular intervals (perhaps every 15 minutes) via a sceduled task. Running the tool does not mean an update is sent as you can configure it to only update when a change is detected.

The tool depends on curl being available. On Windows 10 curl comes preinstalled, which is quite helpful.

Here is an example of a full configuration file for TacitDynDns:

```
; Example full configuration file for TacitDynDns.
;
; This config file is in symbolic-expression syntax.
; Any files or external references are relative to the working dir unless you specify an absolute path.
; Use forward slashes for directory separators.
[
    environment
    [ statefile     TacitDynDns.ips ]           ; Default. This file stores the last IP addresses successfully sent.
    [ logfile       TacitDynDns.log ]           ; Default. This log is appended to. Tells you when updates were applied/attempted.
    [ verbosity     normal ]                    ; Default. Choices are verbose (logs attempts and updates), normal (updates only), and none (no writing to log).
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
; Minimal configuration file for TacitDynDns.

[
    update
    [ domain        your.domain.org ]
    [ service       update.spdyn.de/nic/update ]
    [ username      username ]
    [ password      password ]
]
```

#build instructions

Onpen the solution file in VS 2017 Community and hit build.
