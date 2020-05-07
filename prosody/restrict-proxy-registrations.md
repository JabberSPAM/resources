# Restricting account registrations from open proxies

Spammers are using open proxies on the Internet to register thousands of
XMPP bot accounts to circumvent per-IP registration limits. This guideline
helps to configure prosody in a way that will restrict those accounts and
require manual intervention from the server admin to unlock the restrictions.

Automatically registered spam bots are used to send hundreds or even thousands
of subscription requests (probably as an attempt to circumvent anti-spam
filters) and spam messages. This guideline will restrict accounts coming from
open proxies (as identified by a
[Real-time Blackhole List (RBL)](https://en.wikipedia.org/wiki/Domain_Name_System-based_Blackhole_List))
forbidding them to subscribe to other users and to send messages.

## Required Components

This requires **prosody 0.10** (**TODO** - does it really work on 0.10?) or later, and
**[prosody-modules](https://modules.prosody.im/) from 2020-05-06** or later. A
combination of the following modules is used:

* [mod_register_dnsbl_firewall_mark](https://modules.prosody.im/mod_register_dnsbl_firewall_mark.html)
  to flag account registrations from an open proxy and to send a warning to the user.

* [mod_firewall](https://modules.prosody.im/mod_firewall.html) to restrict
  what flagged accounts may do.

* [mod_admin_telnet](https://prosody.im/doc/modules/mod_admin_telnet) to
  manually unblock accounts.

## Identification of Proxies

There are different providers of RBL lists, with different trade-offs
(false-positive rate, complexity of getting removed, ...). You can choose one
that is most fitting to your situation and feelings, either from the list
below or based on other sources.

However, so far we were not able to identify an RBL provider that only blocks
the proxies used by spammers and not the proxies used by legitimate anonymous
users. 😉

Our approach to this limitation is to give legitimate users a way to get
manually unblocked. This is not perfect, but it is a deliberate trade-off
between becoming a spam relay and providing service to real users.

### Spamhaus XBL

One possibility is to use the
[Spamhaus Exploit and Botnet List XBL](https://www.spamhaus.org/xbl/)
to detect registrations from proxies:

> The Spamhaus Exploits Block List (XBL) is a realtime database of IP
> addresses of hijacked PCs infected by illegal 3rd party exploits, including
> open proxies (HTTP, socks, AnalogX, wingate, etc), worms/viruses with
> built-in spam engines, and other types of trojan-horse exploits.

This includes malware-infected PCs, but also misconfigured proxy servers and
Tor exit nodes. We are aware that blocking Tor has an impact on
[freedom and anonymity](https://github.com/JabberSPAM/jabber-spam-fighting-manifesto/blob/master/Freedom-and-Anonymity.md),
as well as that there is significant controversy around Spamhaus.

Configuration for `prosody.cfg.lua` (see below for details):

```lua
registration_rbl = "xbl.spamhaus.org";
registration_rbl_message = "Your account is restricted because you registered from a malware-infected computer, Tor, or an open proxy. Ask in xmpp:support@conference.example.com?join to get unblocked. More details: https://www.spamhaus.org/query/ip/$ip";
```

There is another name under which the XBL is running, and that is
[Composite Blocking List (CBL)](https://www.abuseat.org/):

> The CBL only lists IPs exhibiting characteristics which are specific to open
> proxies of various sorts (HTTP, socks, AnalogX, wingate, Bagle call-back
> proxies etc) and dedicated Spam BOTs (such as Cutwail, Rustock, Lethic,
> Kelihos, Necurs etc) which have been abused to send spam, worms/viruses that
> do their own direct mail transmission, or some types of trojan-horse or
> "stealth" spamware, dictionary mail harvesters etc.

It uses the exact same database as the XBL, but shows better explanations on
the lookup page.

Configuration for `prosody.cfg.lua` (see below for details):

```lua
registration_rbl = "cbl.abuseat.org";
registration_rbl_message = "Your account is restricted because you registered from a malware-infected computer, Tor, or an open proxy. Ask in xmpp:support@conference.example.com?join to get unblocked. More details: https://www.abuseat.org/lookup.cgi?ip=$ip";
```

The XBL/CBL detected 90% of the 740 IPs used by a spammer on a certain server
on May 1st and 2nd.

### DroneBL

[DroneBL](https://dronebl.org/docs/what) is an RBL often used by IRC
operators, also aiming at blocking proxy servers.

Configuration for `prosody.cfg.lua` (see below for details):

```lua
registration_rbl = "dnsbl.dronebl.org";
registration_rbl_message = "Your account is restricted because you registered from a malware-infected computer, Tor, or an open proxy. Ask in xmpp:support@conference.example.com?join to get unblocked. More details: https://dronebl.org/lookup?ip=$ip";
```
DroneBL detected 76% of the 740 IPs used by a spammer on a certain server
on May 1st and 2nd.


## Operating Principle

When a new account is registered, `mod_register_dnsbl_firewall_mark` will
perform a DNS-based RBL check of the registering IP address. If the check
succeeds (the IP address is blacklisted by the RBL), the module will set a
firewall mark on the account and optionally inform the user.

The firewall mark will then be automatically applied to all logged in sessions
of this user. The prosody firewall will use this mark as a criterion to
restrict what kind of stanzas (messages, presence, subscription requests) are
allowed to be sent by the user. You can define a whitelist of JIDs that are
still allowed to talk.

## Configuration

These are the required files in your prosody deployment, if you are hosting
`example.com`.

### JID Whitelist

Please create a file `/etc/prosody/whitelist_to.txt` that contains the JIDs
that a flagged account may talk to, one on each line:

```
admin@example.com
support@conference.example.com
```

This will be used by the firewall script later on.

### Firewall Rules

You need to create the following firewall script as `/etc/prosody/rbl.pfw`
(you can change the name and the path, but need to reflect it in prosody.cfg
below):

```
%LIST whitelist_to: file:/etc/prosody/whitelist_to.txt

############ preroute chain: stanzas from local users ############
::preroute

# allow errors, whitelisted and self-messages
JUMP_CHAIN=user/pass_acceptable

# process stanzas from marked users
USER MARKED: dnsbl_hit
JUMP_CHAIN=user/marked_user

# you can add further rules to restrict non-marked users as well
# ...


############ chain to pass acceptable messages ############
::user/pass_acceptable

# do not firewall error stanzas, or they'll loop
TYPE: error
PASS.

# accept unsubscribe(d) and unavailable (see #1331)
KIND: presence
TYPE: unsubscribe|unsubscribed|unavailable
PASS.

# do not filter whitelisted receivers
CHECK LIST: whitelist_to contains $<@to|bare>
PASS.

# do not filter stanzas to self
TO SELF?
PASS.


############ quarantine for MARKed users ############
::user/marked_user

# reject outgoing subscriptions, allow MUC and normal presence
KIND: presence
TYPE: subscribe
JUMP_CHAIN=user/bounce_marked

# work around issue #1331
KIND: presence
TYPE: unavailable
PASS.

# allow talking to contacts
SUBSCRIBED?
PASS.

# bounce all non-MUC messages
KIND: message
TYPE: chat|normal|headline
NOT SUBSCRIBED?
JUMP_CHAIN=user/bounce_marked


############ rule to reject spam messages and responses ############
::user/bounce_marked
LOG=spam: marked-user $(stanza)
BOUNCE=not-allowed (Your account is suspended. Contact support.)
```

The last rule will also write each rejected stanza into prosody.log, you can
grep it for `spam: marked-user`

### Prosody config file

The following needs to be added to your `/etc/prosody/prosody.cfg.lua` config
file.

Please replace `support@conference.example.com` with the JID of your support room!

```lua

-- Spamhaus RBL address
registration_rbl = "xbl.spamhaus.org";

-- Automatic message to flagged user account, outlining where to find help
-- as well as why the IP was blocked. You can use the following variables:
--   $ip $username $host
registration_rbl_message = "Your account is restricted because you registered from a malware-infected computer, Tor, or an open proxy. Ask in xmpp:support@conference.example.com?join to get unblocked. More details: https://www.spamhaus.org/query/ip/$ip";

-- Enable firewall marks and load the firewall script
firewall_experimental_user_marks = true;
firewall_scripts = { "/etc/prosody/rbl.pfw" }

-- Add the following two modules to your global or per-host modules_enabled:
modules_enabled = {
	... -- whatever you have there already
	"firewall";
	"register_dnsbl_firewall_mark";
}
```

## Unblocking Accounts

Proper tooling is still TODO.

Manual removal of the firewall mark on an existing user account
(`victim@example.com`), from `mod_admin_telnet`, while the user is connected:

```
>bare_sessions["victim@example.com"].firewall_marks = nil
```

Manual removal if the account is not logged in, from the shell:

```
rm /var/lib/prosody/example%2ecom/firewall_marks/victim.dat
```
