# Collection of antispam server modules

## Ejabberd

### Spam blocking

* [mod_block_strangers](https://docs.ejabberd.im/admin/configuration/modules/#mod-block-strangers) (internal)    
Blocks incoming messages from unknown contacts. Not the recommended way to block spam as it also degrades the 
UX of getting in touch with people using XMPP.

* [mod_pres_counter](https://docs.ejabberd.im/admin/configuration/modules/#mod-pres-counter) (internal)   
Detects presence subscription flood/spam for incoming and outgoing subscriptions.

* [mod_privacy](https://docs.ejabberd.im/admin/configuration/modules/#mod-privacy) (internal)        
Allows to add users to a blocklist by implementing [XEP-0016: Privacy Lists](https://xmpp.org/extensions/xep-0016.html).

## Metronome

## Openfire

## Prosody

### Spam blocking

### Spam reporting

* [mod_server_contact_info](https://prosody.im/doc/modules/mod_server_contact_info) (internal)    
Provides contact addresses by implementing [XEP-0157: Contact Addresses for XMPP Services](https://xmpp.org/extensions/xep-0157.html).
Providing contact addresses is important to give server operators an easy way to get in touch with you
if they receive spam from your server.

* [mod_spam_reporting](https://modules.prosody.im/mod_spam_reporting.html) (community)    
Allows users to report Spam by implementing [XEP-0377: Spam Reporting](https://xmpp.org/extensions/xep-0377.html).
    * [mod_watch_spam_reports](https://modules.prosody.im/mod_watch_spam_reports.html) (community)    
    This module sends a message to the server admins if Spam is reported.

## Tigase

