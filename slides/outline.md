# MCP as a Platform
## What I Learned Building a Portfolio of MCP Servers

Markus van Kempen
Executive Architect & Venture Capitalist in Residence, IBM
MCP Dev Summit Toronto | 25 minutes

**Contact** — [mvankempen@ca.ibm.com](mailto:mvankempen@ca.ibm.com) ·
[markus.van.kempen@gmail.com](mailto:markus.van.kempen@gmail.com) ·
[markusvankempen.github.io](https://markusvankempen.github.io/)

> **Where the real title goes, and where the joke goes.** Slide 1 carries the accepted session
> title verbatim — the words in the Sessionize program and on the schedule board — plus the badge
> title, because that is the slide where the room decides whether I have actually built this stuff.
> The hook ("one server teaches you the protocol…") is the subtitle underneath, not the headline.
> The `Markus · Research · Floor 7½ 🏢🤏` line runs on the closing slide and the contact card,
> where the laugh lands and nobody is calibrating credibility any more.

> Outline mirrors `talk-02-mcp-as-a-platform-portfolio-slides.html`
> (17 talk slides + 5 appendix slides: three legends, how I publish, and a contact card).
> If you change one, change the other.
>
> `talk-02-mcp-as-a-platform-portfolio-slides.pdf` is the exported handout —
> one slide per landscape page. Regenerate it with **File → Print → Save as PDF**
> (the deck ships a print stylesheet), or re-run the export script.

**Naming rule for this deck:** slide bodies describe systems **by domain**, never by product
name. The concrete open-source reference lives in the **footer as a link**, one or two per slide.

**No hard counts on slides.** Server and tool counts keep moving, so the deck says "a handful",
"dozens", "a couple". Say the live number out loud if you want it — don't bake it into a slide.
The only literal count left is `0 tools discovered` on slide 9, which is the bug message.

---

## Slide 1 — MCP as a Platform

**What I Learned Building a Portfolio of MCP Servers**

One server teaches you the protocol. A portfolio teaches you what MCP actually is — six open-source
servers, two editor extensions, and the mistakes I only found by building the next one.

Markus van Kempen · Executive Architect & Venture Capitalist in Residence, IBM

**On screen from the first slide:** [mvankempen@ca.ibm.com](mailto:mvankempen@ca.ibm.com) ·
[markus.van.kempen@gmail.com](mailto:markus.van.kempen@gmail.com) ·
[markusvankempen.github.io](https://markusvankempen.github.io/)


---

## Slide 2 — Different systems. The same questions.

**Domains (chip row):** cloud containers · support ticketing · asset management · quantum circuits · agent orchestration · single-agent bridge

**The six recurring questions — this is the agenda:**

| # | Question | Answered on |
|---|---|---|
| 1 | What do I call this? | Slides 4–6 (tool design, tool overload) |
| 2 | Is it done when it returns 200? | Slide 7 (requester attribution) |
| 3 | Where does this actually run? | Slides 9–10 (local isn't remote, the scars) |
| 4 | What's genuinely reusable? | Slide 12 (copy the boring parts, leave the tools) |
| 5 | Can a developer use it without me? Can a model? | Slides 8, 12, 15 (DX, README, readiness) |
| 6 | How does anyone find it? | Slides 13–14 (discoverability — teased here, detail there) |


---

## Slide 3 — One capability, three ways to run it

Editor extension → MCP server → enterprise API

- **Extension** — setup, visibility, one-click `mcp.json`
- **Local stdio** — fast feedback, credentials stay local
- **Remote SSE** — shared URL, server-side secrets, team and CI

> The extension is the control plane. The MCP server is the capability.

---

## Slide 4 — The first version is deceptively simple

One tool feels like the whole product. Then: one more operation, a safer default, pagination, an error message that says what to do next, and a way to find the server at all.

---

## Slide 5 — At a handful of tools, naming is the interface at dozens

`search_tickets(status, requester_email, limit)` beats `request(path, method, query, body)`.


---

## Slide 6 — More tools didn't mean more capability

**Before:** `query_records`, `query_with_filter`, `query_with_fields`, `query_object_structure`, `query_work_orders`, `query_assets`, `query_locations`, …

**After:** `list_schemas`, `get_schema`, `run_query`, `render_table`, `render_details`, `get_instance`, + a few write tools

> Technically precise. Operationally confusing.


---

## Slide 7 — The hardest tool wasn't the complicated one

`create_ticket` returned `201 Created` every time — and set the requester to the API service account, so the platform mailed every reply to the bot instead of the customer.

> A tool isn't done when the API call succeeds. It's done when the next thing that happens is right.

---

## Slide 8 — A tool is a UI for an agent

An extension is a UI for the developer: **Discover** existing config, **Connect** through one guided flow, **Diagnose** with failures that say what to try.

---

## Slide 9 — Zero tools discovered. No error. No warning.

```
command: node /Users/me/my-mcp/index.js
→ 0 tools discovered

command: node index.js
package-root: ./my-mcp
→ package uploaded, tools discovered
```

Also changes when you deploy: secrets move server-side, diagnostics stop being optional, the hostname isn't stable.


---

## Slide 10 — The failures that taught me the most

| What I saw | What was actually wrong | What I changed |
|---|---|---|
| 0 tools discovered | Laptop path in a cloud container | Upload the package, run from a relative root |
| A vendor error code, not a 404 | Wrong API base URL fails silently | Pin host, assert on startup |
| Auth fine, jobs fail | Missing a required tenant header | Validate headers before first call |
| `mcp.json` stopped working | Platform assigned a new hostname | Resolve endpoint after deploy |
| Model picked wrong tool | A pile of near-identical `query_*` | One query tool + schema discovery |

Four of five failed **silently**.

---

## Slide 12 — Copy the boring parts. Leave the tools alone.

Server two made me want one library for everything. The only things that were actually the same: the folder, the README, how it starts, how it fails. Searching a ticket is not deploying a container.

**Copy this:** same folder and README shape, same env and startup checks, same error shape and logs, same version notes, one test that it starts.

**Don't copy this:** ticket search is not cloud deploy; a read is not a write; your laptop is not a public URL; work login is not a local key.


---

## Slide 13 — The README is part of the API

Seven questions a developer must answer without messaging me: what it does and who for, which tools, install and config, a real call, permissions and risks, what happens on failure, where to report a problem.

---

## Slide 13 — Where does someone find your MCP server?

Today, we find MCP servers through four main channels: **MCP marketplaces** (PulseMCP), **npm**, **GitHub**, and **IDE code extensions**:

1. **MCP Marketplaces** — PulseMCP, MCP Registry, Cursor Directory
2. **npm** — Package registries, PyPI, `npx` instant run
3. **GitHub** — Open-source repos, READMEs, topics & code search
4. **IDE Code Extensions** — VS Code Marketplace, Cursor, Open VSX

Then the concession, because it's true and the room knows it:

- **What actually gets found** — servers with a *product name in the title*. People search the product they already use, not the protocol and not me.
- **What that's worth** — it's a word I don't own. The vendor ships an official server and I'm second. And a server with no famous product in its name gets nothing from any of these four doors.

> The product name is the only index that reliably works — and it's the one thing I didn't build.


---

## Slide 14 — Publishing isn't the same as being findable

What I can publish (source on GitHub, packages on npm/PyPI, MCP marketplace & registry listings, IDE code extensions, docs) vs. what discovery needs (trustworthy identity, searchable capabilities, compatibility, permissions, maintenance signals, **runtime lookup instead of a config file**) vs. the honest answer.

The honest answer, concretely:

- Product-name search works, but only if you already know the product. That's **retrieval, not discovery**.
- The agent can't search at all. It sees its config file; nothing else exists.
- **Nothing left to fix is in my repo** — it's in the spec, the registries, and the clients.


---

## Slide 15 — Can a developer use it without me? Can a model?

Your server has **two readers**, and they read different artifacts.

**The developer reads the README**

- Running in ten minutes — timed, clean machine
- At least one *real call with real output*, not just a parameter table
- Edge cases written down: empty result, rate limit, expired token, partial failure
- Honest about limits, cost, and what it can't do
- Where to report a problem

**The model reads the schema**

- Names that say what they do without the source
- Descriptions that say *when* to use it, not just what it does
- Every parameter with a type, a default, and an example value
- What "no results" means — so it doesn't retry forever
- Errors phrased as next actions, because it will act on them

> The model never opens your README. The description field is the entire manual it gets.


---

## Slide 16 — MCP servers can become more than integrations

Good tools. Clear docs. Honest metadata. A place to find them.

**Honest metadata** — everything a person or an agent reads *before* installing: what it touches,
which permissions it wants, what it was tested against, whether anyone still maintains it.
A `1.0.0` on an abandoned server is the same lie as a `200 OK` on a broken outcome.


---

## Slide 17 — Thank you

Build one. Publish it. Make the next one easier to use.

- [markusvankempen.github.io](https://markusvankempen.github.io/) — the portfolio
- [markusvankempen.github.io/wxo-labs](https://markusvankempen.github.io/wxo-labs/) — 14 hands-on labs, two on MCP

**QUESTIONS?** — and the contact row stays on screen for the whole Q&A:

- [mvankempen@ca.ibm.com](mailto:mvankempen@ca.ibm.com) — work
- [markus.van.kempen@gmail.com](mailto:markus.van.kempen@gmail.com) — personal, open source
- [markusvankempen.github.io](https://markusvankempen.github.io/) — the portfolio

Under the chips: `Markus · Research · Floor 7½ 🏢🤏`

**Right column:** Sessionize feedback QR (`session-feedback-qr.png`). Leave this slide up for Q&A and ask them to scan while you take questions.


---

## Appendix — Backup slides (5 slides, after the close)

These sit after "Questions?" and are **excluded from the slide counter and progress bar**
(`data-appendix="true"`). The deck still reads `17 / 17` on the closing slide; the appendix
shows as `Appendix · 1 / 5`. They cost no talk time and exist for Q&A and for the shared file.

### Legend 1 — The acronyms

| Term | Stands for | Why it's in this talk |
|---|---|---|
| **MCP** | Model Context Protocol | Open standard. Clients discover and call tools; servers wrap APIs. |
| **WXO** | watsonx Orchestrate | IBM's agent orchestration platform. Labs 7–8; two servers in the portfolio. |
| **CRN** | Cloud Resource Name | IBM Cloud's globally unique ID (`crn:v1:…`). New project → new CRN → often a new hostname. |
| **API** | Application Programming Interface | The enterprise system behind the MCP server. A `200` is not a finished tool. |
| **ADK** | Agent Developer Kit | How WXO imports an MCP server as a toolkit. |
| **CE** | Code Engine | IBM Cloud serverless containers. The "cloud containers" domain. |
| **IAM** | Identity and Access Management | Why a missing tenant header fails every job while auth still looks "fine." |


### Legend 2 — How MCP actually talks

| Term | What it is | Where it runs |
|---|---|---|
| JSON-RPC 2.0 | The message format MCP speaks | Both — identical on every transport |
| **stdio** | Client launches your server as a child process, talks over stdin/stdout | Local only — **no port, no URL**; secrets via env vars |
| **SSE** (Server-Sent Events) | `text/event-stream` over HTTP; long-lived `GET` for server→client, separate `POST` for client→server | Remote — **whatever port your HTTP server binds**; **443** over HTTPS in production |
| **Streamable HTTP** | Current remote transport: one endpoint, response can upgrade to an SSE stream; supersedes the older HTTP+SSE pair | Remote — same HTTPS port |
| `tools/list` | The call that enumerates what a server offers | Both — "0 tools discovered" means this came back empty |

Spec: [modelcontextprotocol.io/specification](https://modelcontextprotocol.io/specification) ·
[docs/concepts/transports](https://modelcontextprotocol.io/docs/concepts/transports)

### Legend 3 — Packaging, config and distribution

| Term | What it is | Why it's in this talk |
|---|---|---|
| `mcp.json` | Client config: server name, command or URL, env vars | Lives in `.vscode/` or `.cursor/`; the extension writes it |
| `npx` | Runs an npm package without installing it | Shortest path from "found it" to "called it" |
| package root | Zips and uploads the server dir so a cloud runner uses a relative path | The fix for **0 tools discovered** (slide 9) |
| `/health` `/test` `/admin` | Alive / works / operable — **my convention, not the spec** | What a deployed server needs to explain itself |
| MCP Registry | Community index of published servers | A front door on slide 13 |
| Open VSX | Vendor-neutral marketplace for VS Code-compatible editors | Where extensions land outside the MS marketplace |


### Publish — Ship the package. Then ship the name.

The registry does not host your code. npm does. Publish npm first, or the name publish fails.

1. Bump the version in two files — `package.json` and `server.json`, same number.
2. Publish the package — `npm publish --access public` from `server/`, not the repo root.
3. Wait about a minute. npm is not instant.
4. Publish the name — `mcp-publisher publish`. Then open both pages.

If it has an extension: package the `.vsix` and upload it to Open VSX. The registry name must start with `io.github.youruser/`.

Full checklist: `internal/PUBLISHING.md`.

### Contact

The last slide in the file, so it is what stays on the projector while the room empties.

Under the headline: `Markus · Research · Floor 7½ 🏢🤏`

| Where | Address | For |
|---|---|---|
| Work | [mvankempen@ca.ibm.com](mailto:mvankempen@ca.ibm.com) | IBM — Executive Architect & Venture Capitalist in Residence |
| Old friend | [markus.van.kempen@gmail.com](mailto:markus.van.kempen@gmail.com) | Open source, and anything in this deck |
| Portfolio | [markusvankempen.github.io](https://markusvankempen.github.io/) | Every server and extension this talk refers to |


---

## Footer reference map

Each slide keeps its body generic and carries the concrete link in the footer.

| Slide | Footer reference |
|---|---|
| 1–4, 8, 11, 12, 15, 16 | [markusvankempen.github.io](https://markusvankempen.github.io/) |
| 5, 7 | [github.com/markusvankempen/zendesk-mcp](https://github.com/markusvankempen/zendesk-mcp) |
| 6 | [maximo-mcp-ai-integration-options](https://github.com/markusvankempen/maximo-mcp-ai-integration-options) |
| 9 | [wxo-labs Lab 7 — MCP Basics](https://github.com/markusvankempen/wxo-labs/blob/main/WXO_LABS_TUTORIAL_GUIDE.md#lab-7-mcp-basics---your-first-mcp-server) + [Lab 8 — MCP Advanced](https://github.com/markusvankempen/wxo-labs/blob/main/WXO_LABS_TUTORIAL_GUIDE.md#lab-8-mcp-advanced---user-context--credentials) |
| 10 | portfolio + [wxo-labs troubleshooting guide](https://github.com/markusvankempen/wxo-labs/blob/main/WXO_LABS_TUTORIAL_GUIDE.md#troubleshooting-guide) |
| 13, 14 | [registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io/) |
| 17 | portfolio + [wxo-labs](https://markusvankempen.github.io/wxo-labs/) |
| Appendix · Publish | [npmjs.com/package/mcp-ticket-demo](https://www.npmjs.com/package/mcp-ticket-demo) + [registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io/) |
| Appendix · Contact | [markusvankempen.github.io](https://markusvankempen.github.io/) |

To add or change a reference, edit `data-ref` / `data-ref-url` (and optionally
`data-ref2` / `data-ref2-url`) on the `<section class="slide">` element. The footer
renders them automatically.

---

## Timing Guide

| Slides | Topic | Time |
|---|---|---:|
| 1–3 | Hook, portfolio, architecture | 3 min |
| 4–7 | Tool design + two real scars | 7 min |
| 8–10 | DX, local vs remote, failure table | 5 min |
| 11–12 | Reuse and documentation | 3 min |
| 13–14 | Discoverability (+ demo) | 4 min |
| 15–17 | Checklist, opportunity, close | 3 min |

Total: 25 minutes

## Demo

**One demo only:** slide 13 — search for one of your own servers in a clean browser without using your name. If it fails, the chip list on the slide makes the same point.

Everything else stays as screenshots.

## Deck controls

- `←` / `→` / space — navigate
- `G` or click the counter — jump to a slide number
- `T` — 25-minute elapsed timer (turns coral when over)
- `Cmd`+`P` — one 16:9 page per slide, footers included; save as PDF for the handout

## Backup slides

Appendix material lives in the repos: WxO labs and field guide, Quantum API research, Code Engine deployment guide, asset-management API explorer, ETL extension MCP wizard.
