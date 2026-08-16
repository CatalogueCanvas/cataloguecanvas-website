---
title: Privacy
---

<p class="kicker">Documentation</p>

# Privacy & telemetry

<p class="lead">CatalogueCanvas sends nothing by default. Two optional trackers exist, both anonymous, both switched on separately by the admin.</p>

Nothing here is on when you install the app. If you never touch either setting below, your
instance makes no telemetry request at all, and there is no background process waiting to
change that.

## What is never sent

No hostname. No IP address. No file paths or filenames. No catalogue content, item titles,
notes, tags or images. No user accounts or passwords. No LLM configuration, API URLs or keys.

Events carry a random identifier and a handful of coarse numbers. The identifier is a UUID
generated once and stored at `<CC_DATA_DIR>/instance_id`, the same persist-under-the-data-directory
pattern the app uses for the session signing key. It lets an install ping and a later weekly
ping be recognised as coming from the same instance. Nothing about your machine feeds into
it, and if you delete the file the app mints a new one.

One field is worth spelling out. The operating system is reported as `platform.system()`,
which returns `Linux`, `Darwin` or `Windows` and nothing more. The developers avoided the
full platform string, which carries kernel and glibc versions, because next to a stable
identifier it would work as a fingerprint.

## The install ping

Fires once, on first boot, and only when `CC_INSTALL_TRACKING=1`. Leave the variable unset
and it never runs.

It sends the app version, the git SHA of the build, the install type (`docker` or
`bare-metal`, detected by looking for `/.dockerenv`), and the operating system.

```json
{
  "api_key": "phc_...",
  "event": "catalogue_install",
  "distinct_id": "9f2c1ab84e7d4c0fa1b6e5d3c7a90f21",
  "properties": {
    "version": "0.2.1",
    "git_sha": "3c52d48",
    "install_type": "docker",
    "os": "Linux"
  }
}
```

Once it succeeds, a flag is stored and it never fires again. If the send fails, because the
machine is offline or firewalled, it backs off for a day rather than retrying on every boot.
A container stuck in a restart loop will not hammer anything.

## Weekly usage statistics

Off by default. Turn it on at **Settings → Usage statistics**, and turn it off again in the
same place.

When it is on, it adds three numbers to the same base fields: how many items are in the
catalogue, the size of the database file in bytes, and total system RAM.

```json
{
  "api_key": "phc_...",
  "event": "catalogue_weekly",
  "distinct_id": "9f2c1ab84e7d4c0fa1b6e5d3c7a90f21",
  "properties": {
    "version": "0.2.1",
    "git_sha": "3c52d48",
    "install_type": "docker",
    "os": "Linux",
    "item_count": 428,
    "db_size_bytes": 5242880,
    "ram_total_bytes": 16777216000
  }
}
```

A stored timestamp keeps it to one event per week. There is no scheduler in the app, so the
check rides along with the existing update-check request to `/api/version` rather than
running on a timer of its own. Loading a page more often does not send more events.

## Turning it off, or pointing it somewhere else

Leave `CC_INSTALL_TRACKING` unset, which is the default of `0`, and keep the Settings toggle
off. That is the whole opt-out, and it is the state a fresh install starts in.

To collect the data yourself instead, set `CC_POSTHOG_HOST` and `CC_POSTHOG_KEY` to your own
PostHog project. Events then go to your instance and nowhere else.

!!! note "About the PostHog key in the image"

    The default project key ships in the source and in the published image, and it is public
    on purpose. It is a write-only capture key: it can post events and cannot read them
    back, so publishing it exposes no data. The trade-off is that anyone holding it could
    forge events, which is accepted here because the data is coarse usage counts, not
    anything decisions hinge on. It is not a leaked secret, and there is no need to rotate
    or hide it.

## Where the data goes

Events go to PostHog in the EU region, at `https://eu.i.posthog.com`, unless you have
redirected them.

Telemetry never affects the running app. Every network failure is swallowed, so an
unreachable endpoint, a blackholed connection or a rate-limited response will not break a
boot or slow a request. The install ping uses a short two-second timeout precisely so a dead
network cannot stall startup.

## The activity log is not telemetry

Separately from any of the above, the app keeps an [activity log](admins.md#activity-log)
recording who changed what and when. It sits next to the telemetry here so the two are not
confused, because they are different things.

The activity log is a file on your own disc, under your data directory. Nothing in it is
transmitted, and none of it reaches PostHog or any other service. Unlike the trackers above it
is on by default, since it is a local record rather than a report to anyone. It stores field
names rather than values, so passwords, share tokens, note bodies and the LLM API URL never
appear in it.

## This documentation site

This site is a separate matter from the app. It uses Google Analytics and asks for cookie
consent, the ordinary website kind, which has nothing to do with the copy of CatalogueCanvas
you install. Your instance does not report here, and reading these pages does not touch your
catalogue.

## Related

- [Configuration](install.md#configuration) for the environment variables.
- [Checking for updates](admins.md#checking-for-updates), the other optional feature that
  makes a network request. It reads the public version list from GitHub and sends nothing
  about your instance.
