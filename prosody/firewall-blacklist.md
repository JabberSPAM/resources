# Using the JabberSPAM blocklist with Prosody

Prosody provides a couple of ways to utilize the data from the JabberSPAM project.

Note that both methods detailed here result in your Prosody making periodic network
requests to fetch the current data. As linking directly to raw files on Github is
discouraged and we don't want to get blocked or rate-limited, the scripts fetch the
data via the free CDN service provided by [cdn.jsdelivr.net](https://cdn.jsdelivr.net/).

## Step 1. Install and enable mod_firewall

If you don't have mod_firewall or the community modules repository
set up already, check out the guide for [installing prosody-modules](https://prosody.im/doc/installing_modules#prosody-modules).

Add `"firewall"` to your `modules_enabled` list:

```
modules_enabled = {
  ...
  "firewall";
  ...
}
```

After this step you can restart Prosody or manually load the module using
one of the admin interfaces (e.g. mod_admin_adhoc or mod_admin_telnet/mod_admin_shell).

## Step 2. Enable the firewall rules

There are two ready-made rulesets provided with mod_firewall that utilize
the JabberSPAM data. Choose **one** of them.

### Simple traffic blocking

This ruleset will block all XMPP traffic from servers in the list.

**Pros:** Simple and efficient ruleset, effectively blocks all traffic from listed domains.

**Cons:** Any legitimate traffic from listed servers will also be blocked, even if users
have contacts on them.

```
firewall_scripts = {
    "module:scripts/jabberspam-simple-blocklist.pfw";
}
```

### Smart traffic filtering

This ruleset integrates with the existing [anti-spam ruleset](firewall-custom.md) provided
with mod_firewall. That ruleset attempts to classify incoming traffic as "spam", "not spam"
or "unknown". This ruleset will drop anything in the "unknown" category that is from a server
identified on the JabberSPAM list.

The base anti-spam ruleset is a work in progress and still evolving. If you use it, make sure you
keep your prosody-modules repository updated to take advantage of the latest available rules and
keep on top of changes as the rules are adapted to any changes in spammer behaviour.

**Pros:** Allows legitimate traffic with listed domains (e.g. existing contacts) to pass through,
minimizing impact on users. Integrates well with additional anti-spam rulesets.

**Cons:** Not a 100% block, and some spam may still pass if it can fool the anti-spam rules.

```
firewall_scripts = {
    "module:scripts/spam-blocking.pfw"; -- Base anti-spam ruleset
    "module:scripts/spam-blocklists.pfw"; -- Add blocklist-based filter
}
```

**Note:** spam-blocklists.pfw will have no effect unless you also load the base ruleset,
spam-blocking.pfw!

## Step 3. Reload Prosody

Reload prosody, e.g. `prosodyctl reload` to apply firewall rule changes now and after future ruleset
updates.

Changes to the blocklist data itself will be fetched automatically - there is no need to trigger this
manually.

