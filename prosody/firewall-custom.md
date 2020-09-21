# Creating custom anti-spam rules with mod_firewall

Prosody's mod_firewall comes with a ready-made anti-spam ruleset that you can
use to tackle incoming spam on your server. The default ruleset is conservative
about what it blocks. In other words, if there is a chance that a message *may*
be legitimate, it will allow it by default.

The ruleset can be extended in various ways to override its
default behaviour. This document demonstrates how to do this.

## Overview

The base ruleset attempts to categorize incoming stanzas into one of three categories:

- spam
- not spam
- unknown

By default traffic in the "unknown" category is allowed to pass. However it is possible
to define custom handling for this traffic and apply further rules.

One example of a useful extension is checking "unknown" traffic against
the JabberSPAM blocklist. See the [how-to](firewall-blacklist.md) for
more info.

### Built-in custom chains

The built-in rules expose a number of extension points where you can add additional
rules to classify traffic or override default behaviour. All these chains end with
the '_custom' suffix. Chains that do not end with '_custom' should **not** be modified.

#### user/spam_check_custom

Apply additional rules to all stanzas *before* they are checked by the built-in rules.
Mainly useful to PASS stanzas that you do not want to be filtered.

#### user/spam_check_message_custom

Apply additional rules to messages from strangers, aiming to PASS stanzas that are not
spam and jump to ::user/spam_reject for stanzas that are considered spam.

#### user/spam_check_message_content_custom

Apply additional rules to messages that may be spam, based on message content rules.
These may contain more intensive rules, so are executed after all other checks. Rules
should jump to the 'user/spam_reject' chain if a message is considered spam.

#### user/spam_check_presence_custom

Apply additional rules to presence that may be spam.

#### user/spam_check_subscription_request_custom

Apply additional rules to subscription requests.

#### user/spam_handle_unknown_custom

Override default handling of stanzas that weren't explicitly passed or rejected by the
anti-spam checks. By default unknown stanzas are allowed through, but you may want to
override this default (e.g. jump unconditionally to 'user/spam_reject') or apply further
scrutiny to these stanzas (e.g. check against external blocklists).

#### user/spam_reject_custom

Override default handling of stanzas that have been recognised as spam (default is to bounce
a policy-violation error). 

## Examples

### Blocking all unknown traffic

Although we don't necessarily recommend this approach, it serves as
a good starter example. Let's see how to reject all "unknown" traffic:

```
::user/spam_handle_unknown_custom

JUMP CHAIN=user/spam_reject
```

This inserts a single rule into the 'user/spam_handle_unknown_custom' rule chain. That single
rule immediately jumps to the built-in chain called 'user/spam_reject' which is the built-in chain
that deals with stanzas that should be rejected as spam.

### Block unknown stanzas to specific accounts

This snippet will do the same as above, but configurable on a per-account basis:

```
%LIST protected_accounts: file:/etc/prosody/protected_accounts.txt (missing: ignore)

::user/spam_handle_unknown_custom

CHECK LIST: protected_accounts contains $<@to|bare>
JUMP CHAIN=user/spam_reject
```

In `/etc/prosody/protected_accounts.txt` you would put a simple list of JIDs:

```
someuser@example.com
other@example.com
```

**Tip:** To reverse this (e.g. block unknown stanzas addressed to everyone on
the server, but let them through to listed users) simply change `CHECK LIST`
to `NOT CHECK LIST` (and you should probably also rename 'protected_accounts' to
'unprotected_accounts' to avoid confusion!).

### Blocking all messages containing a specific word

```
::user/spam_check_message_content

# Reject all messages with the word 'advertising' in the body
INSPECT: body#~=advertising
JUMP CHAIN=user/spam_reject
```

See the documentation for the [INSPECT](https://modules.prosody.im/mod_firewall#inspect) condition
and the documentation of [Lua patterns](https://www.lua.org/manual/5.2/manual.html#6.4.1) for the
regex-like language used for matching.

### Drop spam instead of bouncing

The default behaviour when rejecting spam is to send an error back to the sender. Although
this can be useful, especially to give the sender feedback in case of a mistake, you may want
to not reveal that you are using any spam filtering.

This example overrides the default behaviour to discard spam:

```
::user/spam_reject_custom

LOG=Discarding suspected spam: $(stanza:top_tag())
DROP.
```


