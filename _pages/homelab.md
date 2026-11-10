---
layout: single
title: "Homelab"
permalink: /homelab/
author_profile: true
---

<div class="terminal-hero">
  <div class="terminal-window">
    <div class="terminal-header">
      <span class="dot dot-red"></span>
      <span class="dot dot-yellow"></span>
      <span class="dot dot-green"></span>
      <span class="terminal-title">stamp@homelab:~/infra</span>
    </div>
    <div class="terminal-body">
      <div class="terminal-line">
        <span class="prompt">$</span>
        <span class="cmd">&nbsp;hostnamectl</span>
      </div>
      <div class="terminal-line output">Hostname: truenas</div>
      <div class="terminal-line output">OS: TrueNAS CE &nbsp;·&nbsp; ZFS &nbsp;·&nbsp; Docker</div>
      <div class="terminal-line output">Roles: storage, containers, media, backups</div>
      <div class="terminal-line">&nbsp;</div>
      <div class="terminal-line">
        <span class="prompt">$</span>
        <span class="cmd">&nbsp;zpool&nbsp;list</span>
        <span class="arg">&nbsp;-o&nbsp;name,size,health</span>
      </div>
      <div class="terminal-line output">NAME&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SIZE&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;HEALTH</div>
      <div class="terminal-line output green">tank&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3&nbsp;×&nbsp;12&nbsp;TB&nbsp;RAIDZ1&nbsp;&nbsp;ONLINE</div>
      <div class="terminal-line output green">apps-pool&nbsp;&nbsp;441&nbsp;GB&nbsp;SSD&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ONLINE</div>
      <div class="terminal-line">&nbsp;</div>
      <div class="terminal-line">
        <span class="prompt">$</span>
        <span class="cursor"></span>
      </div>
    </div>
  </div>
</div>

The homelab is where I get to break things. ZFS, container operations by hand, backup and
restore paths, and the actual maintenance cost of services you depend on every day — none of
that fits in a work ticket, so it lives here.

Two machines: a TrueNAS box that runs almost everything, and an aging 2-bay Synology that holds
the data I could not re-download if it died.

---

<div class="section-divider">
  <span class="section-prompt">$&nbsp;cat&nbsp;/etc/two-host-split.conf</span>
  <div class="section-line"></div>
</div>

<div class="arch-grid">
<pre class="arch">DS720+ (Synology, J4125)
|-- Vol1   8 TB   7.0T used
|   photos, videos, Immich
|-- Vol2  10 TB   2.0T used
|   &lt;- Hyper Backup from Vol1
|   &lt;- nightly rsync of TrueNAS appdata</pre>
<pre class="arch">TrueNAS CE (i3-10100, 32 GB)
|-- tank       3 × 12 TB RAIDZ1   ~21.7 TiB
|   media library, downloads, retro library
`-- apps-pool  441 GB SSD
    appdata, compose files, databases</pre>
</div>

Both machines follow the same contract: pinned tags, a manual upgrade window on the box that
holds the photos, and a database dump before anything versioned changes.

The rule is deliberately backwards: **irreplaceable data sits on the slow box that never gets
touched, and anything rebuildable sits on the fast box I break constantly.** Most people put
everything on the newest hardware. I would rather the tinkering happen where a mistake costs me
an evening of re-downloading, not twenty years of photos.

That split decides everything else: the Synology gets pinned image tags and a manual update
window, while TrueNAS is free to be rebuilt from scratch in about an hour.

---

<div class="section-divider">
  <span class="section-prompt">$&nbsp;docker&nbsp;ps&nbsp;--format&nbsp;table</span>
  <div class="section-line"></div>
</div>

| Service | What it does | Runs on |
|---|---|---|
| **TrueNAS CE** | ZFS storage + container host; datasets, snapshots, replication | host |
| **Dockge** | compose stack management — every stack is a directory with `compose.yaml` + `.env` | container |
| **Jellyfin** | media server, client for every screen in the house | container |
| **Immich** | photo library — my replacement for Google Photos, own hardware, own data | Synology |
| **RomM** | retro game library and metadata, playable through the browser | container |
| **BookOrbit** | e-book library with KOReader sync (Postgres + pgvector) | container |
| **Homarr** | one dashboard for everything above | container |
| **Hermes Agent** | self-hosted AI agent, so the assistant runs on my hardware too | container |
| **cloudflared** | outbound-only tunnel to publish exactly one service, no open inbound ports | container |

Everything on that list is open source and self-hosted. No subscriptions, no vendor deciding
whether my photo library still exists next year.

---

<div class="section-divider">
  <span class="section-prompt">$&nbsp;cat&nbsp;principles.md</span>
  <div class="section-line"></div>
</div>

<div class="exp-card">
  <div class="exp-role">01 — Pin the image tag. Always.</div>
  <div class="exp-desc">Nothing stateful runs on <code>latest</code>. If a version cannot be rolled back — Immich, Postgres, MariaDB — the tag is pinned and the upgrade is a decision, not a side effect of a container restart.</div>
</div>

<div class="exp-card">
  <div class="exp-role">02 — Snapshot first, then copy.</div>
  <div class="exp-desc">Never rsync a live database directory. Copying a running Postgres or SQLite tree captures torn pages and produces a backup that will not restore. Take a ZFS snapshot, then rsync out of <code>.zfs/snapshot/&lt;snap&gt;/</code>.</div>
</div>

<div class="exp-card">
  <div class="exp-role">03 — Let the app write its own dumps.</div>
  <div class="exp-desc">Immich restores from the logical dumps it writes to <code>upload/backups</code> every night, not from a copied data directory. A crash-consistent copy is not the same thing as a restorable backup, and the difference only shows up on the day you need it.</div>
</div>

<div class="exp-card">
  <div class="exp-role">04 — Secrets out of YAML.</div>
  <div class="exp-desc"><code>.env</code> next to <code>compose.yaml</code>, interpolated with <code>${VAR:?message}</code>. A missing value should kill the container start with a readable error, not boot a half-configured service that fails a week later.</div>
</div>

<div class="exp-card">
  <div class="exp-role">05 — Freeze the mount paths.</div>
  <div class="exp-desc">Media apps store absolute container paths in their own databases. Renaming <code>/movies</code> to <code>/data/movies</code> marks the entire library as missing, because the database is still pointing at the old path. The host side is one filesystem, so downloads and library share hardlinks and nothing gets copied twice.</div>
</div>

<div class="exp-card">
  <div class="exp-role">06 — One network, no exposed admin UIs.</div>
  <div class="exp-desc">Every container joins one shared Docker network and resolves the others by name — no hardcoded IPs between services. Admin interfaces stay on the LAN; exactly one thing is published to the internet, through an outbound tunnel.</div>
</div>

---

<div class="section-divider">
  <span class="section-prompt">$&nbsp;cat&nbsp;backup-plan.md</span>
  <div class="section-line"></div>
</div>

| Chain | Source | Method | Frequency |
|---|---|---|---|
| Photos &amp; videos | Synology Vol1 | Hyper Backup → Vol2 | daily |
| App configs &amp; databases | TrueNAS `apps-pool` | ZFS snapshot → rsync → Vol2 | daily |
| Immich | app-native dumps | `upload/backups`, kept 14 deep | daily 02:00 |

<blockquote>
<p>What is still wrong, written down on purpose: there is no off-site copy yet, both backup chains
share one target, Volume2 holds 2.0 T against a 3.8 T source so its completeness is unverified,
and no restore has been test-fired. A backup you have never restored is a hypothesis, not a backup
— this is the next thing I fix.</p>
</blockquote>

---

<div class="section-divider">
  <span class="section-prompt">$&nbsp;ls&nbsp;hardware/</span>
  <div class="section-line"></div>
</div>

<div class="exp-card">
  <div class="exp-role">TrueNAS CE — the workhorse</div>
  <div class="exp-company">i3-10100 · 32 GB RAM · 3 × 12 TB RAIDZ1 + SSD pool</div>
  <div class="exp-period">storage · containers · media · backups</div>
  <div class="exp-desc">Runs the whole application stack from a Dockge-managed set of compose directories. Rebuildable by design: configs live on the SSD pool and everything else can be re-downloaded.</div>
</div>

<div class="exp-card" style="border-left-color:#79c0ff;">
  <div class="exp-role">Synology DS720+ — the safe</div>
  <div class="exp-company" style="color:#3fb950;">2 bays · 8 TB + 10 TB · Immich, photos, videos</div>
  <div class="exp-period">least-touched machine in the house</div>
  <div class="exp-desc">Not being expanded — it is on its way to becoming a backup target. Pinned tags, quarterly updates, a database dump before every version bump. Boring is the feature.</div>
</div>

<div class="exp-card" style="border-left-color:#d2a8ff;">
  <div class="exp-role">Workstation — the lab bench</div>
  <div class="exp-company" style="color:#d2a8ff;">RTX 4070 Ti · Windows + WSL · models, experiments, hardware projects</div>
  <div class="exp-period">where new ideas get tested before they touch the NAS</div>
  <div class="exp-desc">Fine-tuning, inference and hardware experiments. If something survives a week here, it earns a compose file on the server.</div>
</div>

---

<div class="section-divider">
  <span class="section-prompt">$&nbsp;cat&nbsp;next-steps.txt</span>
  <div class="section-line"></div>
</div>

<div class="skill-grid">
  <span class="skill-tag cloud">off-site backup target</span>
  <span class="skill-tag cloud">test-fired restore drill</span>
  <span class="skill-tag data">monitoring + alerting</span>
  <span class="skill-tag devops">a README per stack</span>
</div>

If you run something similar — ZFS, self-hosted media, a stack you rebuild for fun — I am always
happy to compare notes: [chanowat.tan@gmail.com](mailto:chanowat.tan@gmail.com).
