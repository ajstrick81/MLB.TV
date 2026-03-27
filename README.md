# MLB.TV
Block, Bypass, Filter MLB.TV Ads using AdGuard
This ruleset operates as a DNS-only layer via AdGuard Home (AGH) targeting MLB.TV's ad decisioning, SSAI manifest endpoints, and telemetry infrastructure — without touching core playback delivery.

How it works: Rather than returning NXDOMAIN, ad decisioning and telemetry domains are null-routed using dnsrewrite=0.0.0.0, returning a valid DNS response that points to a dead address. The ad SDK and tracking infrastructure receive what appears to be a successful resolution, but all subsequent connection attempts silently fail. This prevents the aggressive retry behavior that NXDOMAIN typically triggers, keeping the player stable while the ad pipeline goes dark.

MLB.TV's SSAI system routes ad manifest requests through specific API endpoints — /api/v*/manifest*, /api/v*/playback*, and /api/v*/content* — which are intercepted at the DNS layer before they can reach the ad stitching server. With no valid response from the ad decisioning layer, the player falls through to the main content stream. Telemetry platforms including Conviva, Omniture, Scorecard Research, and Publica are blocked in the same pass, cutting off audience measurement and impression reporting entirely.

Core playback infrastructure — playback-heartbeat.mlb.com, mlbstatic.com, Fastly CDN, and Google Video — is explicitly allowlisted to ensure stream delivery, authentication, and heartbeat signals remain completely unaffected.

This repo is updated as often as I can get to it. MLB.TV periodically rotates API paths and ad server endpoints, so it's an ongoing process. All testing and feedback is appreciated.
