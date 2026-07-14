# Home Assistant tibber custom-component override

Copy of `homeassistant/components/tibber` from core 2026.6.4 with local fixes,
deployed to `/config/custom_components/tibber` on the home HA instance. It shadows
the core integration until deleted.

Changes vs core 2026.6.4 (`__init__.py`, `manifest.json`):

1. `manifest.json` requires this fork's pyTibber 0.37.6.post1 via a pinned
   commit tarball with the legacy `#pyTibber==...` fragment (a bare
   `pyTibber @ url` requirement is treated as satisfied by ANY installed
   version and would never install).
2. `async_unload_entry` / the STOP handler disconnect the *cached* client under
   `asyncio.timeout(10)` instead of refreshing OAuth mid-teardown (the
   FAILED_UNLOAD deadlock, home-assistant/core#176268).
3. Phantom-platform protection (home-assistant/core#166228): unload only
   platforms actually registered in the domain's EntityComponent, and log an
   error at setup if a forwarded platform silently failed to register.

Rollback on HA: delete `/config/custom_components/tibber` and restart.
