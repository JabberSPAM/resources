# Collection of antispam server modules

## Table of Contents

1. [Ejabberd](#ejabberd)    
2. [Metronome](#metronome)    
3. [Openfire](#openfire)    
4. [Prosody](#prosody)    
5. [Tigase](#tigase)    

## Ejabberd

### Spam prevention

* [Captcha](https://docs.ejabberd.im/admin/configuration/basic/#captcha) (official)    
Requires a Captcha for in-band registrations which might help to reduce the registration of spam accounts.

### Spam blocking

* [mod_block_strangers](https://docs.ejabberd.im/admin/configuration/modules/#mod-block-strangers) (official)    
Blocks incoming messages from non-contacts. Makes it impossible for people to contact a user without 
exposing presence. Also makes it impossible to distinguish legitimate subscription requests from spambots.

* [mod_pres_counter](https://docs.ejabberd.im/admin/configuration/modules/#mod-pres-counter) (official)   
Detects presence subscription flood/spam for incoming and outgoing subscriptions.

* [mod_privacy](https://docs.ejabberd.im/admin/configuration/modules/#mod-privacy) (official)        
Allows to add users to a blocklist by implementing [XEP-0016: Privacy Lists](https://xmpp.org/extensions/xep-0016.html).

* [mod_spam_filter](https://github.com/processone/ejabberd-contrib/tree/master/mod_spam_filter) (contrib)    
Filters spam messages and subscription requests received from remote servers based on known heuristics.

### Spam reporting

* [mod_disco](https://docs.ejabberd.im/admin/configuration/modules/#mod-disco) (official)    
The option *server_info* provides contact addresses by implementing [XEP-0157: Contact Addresses for XMPP Services](https://xmpp.org/extensions/xep-0157.html).
Providing contact addresses is important to give server operators an easy way to get in touch with you
if they receive spam from your server.

## Metronome

### Spam prevention

* [mod_register_api](https://metronome.im/documentation/configuration) (official)    
Allows to build a challenge and verification system for user registrations and can check for disposable
email addresses.

* [mod_spim_block](https://metronome.im/documentation/configuration) (official)    
This module contains several spam fighting measures like captchas, spam blocking, serving challenges and
more.

### Spam blocking

* [mod_spim_block](https://metronome.im/documentation/configuration) (official)    
This module contains several spam fighting measures like captchas, spam blocking, serving challenges and
more.

* [Blocking Command](https://metronome.im/supported-xeps-list) (official)        
Allows to add users to a blocklist by implementing [XEP-0191: Blocking Command](https://xmpp.org/extensions/xep-0191.html).

### Spam reporting

* [mod_disco](https://metronome.im/documentation/configuration) (official)    
The option *contact_info* provides contact addresses by implementing [XEP-0157: Contact Addresses for XMPP Services](https://xmpp.org/extensions/xep-0157.html).
Providing contact addresses is important to give server operators an easy way to get in touch with you
if they receive spam from your server.

## Openfire

### Spam blocking

* [blacklistspam plugin](https://www.igniterealtime.org/projects/openfire/plugin-archive.jsp?plugin=blacklistspam) (official plugin)    
The plugin looks up a URL containing addresses of known SPAM distributors and blocks all messages sent by
these addresses.

## Prosody

### Spam prevention

* [mod_register_dnsbl_firewall_mark](https://modules.prosody.im/mod_register_dnsbl_firewall_mark.html) (community)    
Flags account registrations from an open proxy. A WIP documentation can be found [here](https://github.com/JabberSPAM/resources/blob/master/prosody/restrict-proxy-registrations.md).

* [mod_captcha_registration](https://modules.prosody.im/mod_captcha_registration.html) (community)    
Requires a Captcha for in-band registrations which might help to reduce the registration of spam accounts.

### Spam blocking

* [mod_block_strangers](https://modules.prosody.im/mod_block_strangers.html) (community)    
Blocks incoming messages from non-contacts. Makes it impossible for people to contact a user without 
exposing presence. Also makes it impossible to distinguish legitimate subscription requests from spambots.

* [mod_blocklist](https://prosody.im/doc/modules/mod_blocklist) (official)        
Allows to add users to a blocklist by implementing [XEP-0191: Blocking Command](https://xmpp.org/extensions/xep-0191.html).

* [mod_firewall](https://modules.prosody.im/mod_firewall.html) (community)    
A powerful tool that enables you to block, bounce, drop, forward, copy, redirect stanzas and more based
on rule scripts. See the [how-to for using the JabberSPAM blocklist](prosody/firewall-blacklist.md) with mod_firewall,
and also the documentation for developing [custom anti-spam rules](prosody/firewall-custom.md).

* [mod_s2s_blocklist](https://modules.prosody.im/mod_s2s_blacklist) (community)    
A module to block connections to remote servers at the s2s level.

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

* [Account Registration Limits](https://docs.tigase.net/tigase-server/8.0.0/Administration_Guide/html/#accountRegLimit) (official)    
Apply a limit on number of account registrations per second.

* [IBR Captcha](https://docs.tigase.net/tigase-server/master-snapshot/Administration_Guide/html/#_captcha_system_now_available_for_in_band_registration) (official)    
Requires a Captcha for in-band registrations which might help to reduce the registration of spam accounts.

### Spam blocking

* [Tigase SPAM Filter](https://docs.tigase.net/tigase-server/8.0.0/Administration_Guide/html/#_tigase_spam_filter) (official)    
The Tigase SPAM Filter project contains additional features provided for Tigase XMPP Server to reduce number of sent/received SPAM messages.

* [Blocking Command](https://docs.tigase.net/tigase-server/master-snapshot/Administration_Guide/html/#blockingCommand) (official)        
Allows to add users to a blocklist by implementing [XEP-0191: Blocking Command](https://xmpp.org/extensions/xep-0191.html).

* [Domain filter domains](https://docs.tigase.net/tigase-server/master-snapshot/Administration_Guide/html/#_add_new_item_vhost) (official)     
Allows to restrict or filter traffic from certain servers.

### Spam reporting

* [Abuse Contacts](https://docs.tigase.net/tigase-server/master-snapshot/Administration_Guide/html/#_abuse_contacts) (official)    
Provides contact addresses by implementing [XEP-0157: Contact Addresses for XMPP Services](https://xmpp.org/extensions/xep-0157.html).
Providing contact addresses is important to give server operators an easy way to get in touch with you
if they receive spam from your server.
