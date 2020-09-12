# Collection of antispam server modules

## Ejabberd

### Spam blocking

* [mod_block_strangers](https://docs.ejabberd.im/admin/configuration/modules/#mod-block-strangers) (official)    
Blocks incoming messages from unknown contacts. Not the recommended way to block spam as it also degrades the 
UX of getting in touch with people using XMPP.

* [mod_pres_counter](https://docs.ejabberd.im/admin/configuration/modules/#mod-pres-counter) (official)   
Detects presence subscription flood/spam for incoming and outgoing subscriptions.

* [mod_privacy](https://docs.ejabberd.im/admin/configuration/modules/#mod-privacy) (official)        
Allows to add users to a blocklist by implementing [XEP-0016: Privacy Lists](https://xmpp.org/extensions/xep-0016.html).

* [mod_spam_filter](https://github.com/processone/ejabberd-contrib/tree/master/mod_spam_filter) (contrib)    
Filters spam messages and subscription requests received from remote servers based on lists of known spammer
JIDs and/or URLs mentioned in spam messages.

### Spam reporting

* [mod_disco](https://docs.ejabberd.im/admin/configuration/modules/#mod-disco) (official)    
The option *server_info* provides contact addresses by implementing [XEP-0157: Contact Addresses for XMPP Services](https://xmpp.org/extensions/xep-0157.html).
Providing contact addresses is important to give server operators an easy way to get in touch with you
if they receive spam from your server.

## Metronome

## Openfire

### Spam blocking

* [blacklistspam plugin](https://www.igniterealtime.org/projects/openfire/plugin-archive.jsp?plugin=blacklistspam) (official plugin)    
The plugin looks up a URL containing addresses of known SPAM distributors and block all messages sent by
these addresses.

## Prosody

### Spam prevention

* [mod_register_dnsbl_firewall_mark](https://modules.prosody.im/mod_register_dnsbl_firewall_mark.html) (community)    
Flags account registrations from an open proxy. A WIP documentation can be found [here](https://github.com/JabberSPAM/resources/blob/master/prosody/restrict-proxy-registrations.md).

### Spam blocking

* [mod_block_strangers](https://modules.prosody.im/mod_block_strangers.html) (community)    
Blocks incoming messages from unknown contacts. Not the recommended way to block spam as it also degrades the 
UX of getting in touch with people using XMPP.

* [mod_blocking](https://modules.prosody.im/mod_blocking.html) (community)        
Allows to add users to a blocklist by implementing [XEP-0191: Blocking Command](https://xmpp.org/extensions/xep-0191.html).

* [mod_firewall](https://modules.prosody.im/mod_firewall.html) (community)    
A powerful tool that enables you to block, bounce, drop, forward, copy, redirect stanzas and more based
on rule scripts.

### Spam reporting

* [mod_server_contact_info](https://prosody.im/doc/modules/mod_server_contact_info) (official)    
Provides contact addresses by implementing [XEP-0157: Contact Addresses for XMPP Services](https://xmpp.org/extensions/xep-0157.html).
Providing contact addresses is important to give server operators an easy way to get in touch with you
if they receive spam from your server.

* [mod_spam_reporting](https://modules.prosody.im/mod_spam_reporting.html) (community)    
Allows users to report spam by implementing [XEP-0377: Spam Reporting](https://xmpp.org/extensions/xep-0377.html).
    * [mod_watch_spam_reports](https://modules.prosody.im/mod_watch_spam_reports.html) (community)    
    This module sends a message to the server admins if spam is reported.

## Tigase

### Spam prevention

* [Account Registration Limits](https://docs.tigase.net/tigase-server/8.0.0/Administration_Guide/html/#accountRegLimit)    
Apply a limit on number of account registrations per second.

### Spam blocking

* [Tigase SPAM Filter](https://docs.tigase.net/tigase-server/8.0.0/Administration_Guide/html/#_tigase_spam_filter)    
The Tigase SPAM Filter project contains additional features provided for Tigase XMPP Server to reduce number of sent/received SPAM messages.

