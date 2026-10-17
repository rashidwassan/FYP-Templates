# 30 · Rabta — Messaging When the Network Is Down (idea #93)

**Offline-first emergency messaging for families, volunteers and village committees, in Urdu and English.** Phones pair with no internet and no server (WebRTC data channels whose connection details are squeezed into a QR code), carry each other's signed messages store-and-forward until they reach people, keep shared boards for missing persons, needs and resources that merge without conflicts, and run a community SOS with responders, triage, a coordinator view and a household roll call. When there is no Wi-Fi at all, very short messages can travel as sound, or over a LoRa radio on USB. A mesh simulator compares routing strategies on the same code for the FYP evaluation chapter. رابطہ اردو اور انگریزی دونوں میں ہے، اردو میں مکمل دائیں سے بائیں ترتیب کے ساتھ۔

**Scale:** 8,720 lines in one `index.html` (≈ 7,390 lines of JavaScript plus the shared 526-line QR encoder, ≈ 630 lines of CSS) · 13 routed modules (Chats, Community SOS with 4 views, Boards with 5 views, Mesh with live view and simulator, Connect, Sound link, Radio bridge, Offline map, Identity, Diagnostics, Notifications, Settings, About) plus the SOS sheet and alert, search palette, tour, shortcuts sheet and two print layouts · ≈ 920 bilingual strings · 117 sprite icons · seed of 8 simulated villagers each with their own ECDSA and ECDH key pairs, 32 signed messages (5 end-to-end encrypted direct messages, 3 SOS calls, 2 locations, a photo, a check-in) and 2 receipts, 4 missing persons, 6 needs, 6 resources, 3 SOS incidents, 16 people in 5 households on the roll call, 7 volunteer responders, 1 merged conflict and 3 notifications · 7 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge or Firefox. It works from `file://` and offline; only Google Fonts (Geologica, Space Mono, Noto Nastaliq Urdu) and, on request, Leaflet 1.9.4 come from the network. The first start creates this phone's keys and a fictional demo village (a second or two).
- **Demo without a second phone:** the eight villagers of "Barkat Abad (fictional)" live inside the page with their own keys and stores. Mesh → *Walk through the village*, or *Meet* on any peer, runs the real sync protocol with them. With the village simulation on, they also meet each other, post now and then, and respond to SOS calls.
- **Two phones:** both on the same Wi-Fi or one phone's hotspot (no internet needed). Phone A: Connect → *Create invite* and show the QR. Phone B: *Scan or paste an invite*, then show its answer QR back. QR scanning uses `BarcodeDetector` (Chrome on Android); elsewhere paste the text code (about 130 characters). Turn off client isolation on the router if pairing fails.
- **Two nodes on one laptop:** Connect → *Open a second node in a new tab* (`?node=B`). Each tab is its own node with its own identity and storage, linked over `BroadcastChannel`. Connect → *Run self-test* runs two real `RTCPeerConnection`s in one page.
- **Needs permission or a secure origin:** camera (QR scanning), microphone (voice notes, sound link listening), Geolocation (otherwise the demo position is used and labelled "simulated"). Web Serial (radio bridge) needs desktop Chrome or Edge on `https://` or `localhost`: run `npx serve .` in this folder. Without it, use the simulated radio.
- **SOS:** press and hold the orange button (nav rail on desktop, floating on phones) for 1.5 s, or focus it with `S` and hold Space. A sheet opens with your last details and a 5-second countdown that pauses as soon as you edit.
- **Where data lives:** `localStorage` under `rabta.` (or `rabta.B.` … for extra tab nodes): `settings`, `identity` (includes private keys), `profile`, `contacts`, `store`, `boards`, `clock`, `conflicts`, `inbox`, `log`, `ui`, `sim`, `village`, `meta`, `lang`.
- **Reset:** Settings → *Reset to demo*, *Demo data* off (keeps your identity, clears the village), *Export / Import JSON*, or the panic option *Wipe this device* (type WIPE; erases every Rabta key on the device, all tab nodes included).

## The problem

When roads, power and mobile networks fail together, people still need to say "we are safe", "we need a boat" or "Bilal is with us" to whoever is nearby, and committees need one shared picture of who is missing and what is needed.

- Glacial lake outburst floods (GLOFs) and flash-flood alerts cut roads in Gilgit-Baltistan for days in 2026 ([Dawn](https://www.dawn.com/news/2018510)), and the NDMA listed GLOF-risk areas in Gilgit-Baltistan and Khyber Pakhtunkhwa for March–September 2026 ([Zameen News](https://www.zameen.com/news/gb-kp-at-risk-of-glacial-floods-ndma.html)).
- Cheap LoRa radios already carry text with no cell network at all ([Meshtastic](https://homepage.meshtastic.org/)), but most families only have phones.
- Local-first software keeps working offline and merges edits later with CRDTs ([Automerge](https://automerge.org/docs/hello/)); Rabta builds its own small CRDTs so students can see every step.

Rabta turns the phones people already carry into a delay-tolerant mesh: messages wait on whichever phone is closest to the people who need them.

## What is built

| Area | Features |
|---|---|
| Connect (core) | Serverless WebRTC data channels with `iceServers: []`; SDP compressed to ICE ufrag/password (6 bits a character), DTLS fingerprint, setup role and host candidates (IPv4, IPv6 or mDNS UUID names) with a CRC-16, then RFC 4648 base32 so the QR uses the alphanumeric mode (≈ 587-character SDP → 129-character code, QR version 5); full SDP rebuilt on the other side; scan with BarcodeDetector or paste; connection states, 45 s watchdog, retry, per-connection log, ping; same-device tabs over BroadcastChannel with heartbeats and a duplicate-tab warning; in-page loopback self-test (codec round trip, data channel, 20 pings, 2 MB transfer with SHA-256 check, the protocol to a throwaway twin identity) |
| Messaging | Channels #village, #medical, #rescue, #lost-found and direct chats; text, location (Geolocation with accuracy, labelled fallback), photo (canvas-compressed JPEG ≈ 10–20 KB), voice note (MediaRecorder, 15 s cap), "I am safe" check-in (also marks your roll-call row), priority flag, SOS; delivery and read receipts for direct chats; 8 bilingual quick messages (both languages by default); signature badge, lock, hop count and time on every bubble; hop-path sheet with relays, TTL left, size, and which simulated villagers hold a copy |
| Store-and-forward (core) | Per-node message store; hello with signed identity proof; summary vectors as id lists or a Bloom filter above 200 ids; only missing copies sent; hop limit, TTL, priority ordering (SOS first) and duplicate refusal; buffer cap that evicts the oldest low-priority copy; gossip of new arrivals to other open links; queue of your messages still waiting for a peer |
| Mesh simulator (core, evaluation) | 10–100 random-waypoint nodes in a square area with a radio range; direct delivery, flooding, epidemic with TTL and binary spray-and-wait run the same `planTransfer` / `storeAccept` code as the phones on the same seeded trace; delivery ratio, SOS delivery ratio, mean and p90 latency, transmissions, overhead, mean hops, peak buffer; bar charts, delivered-over-time and latency CDF line charts, results table, CSV export, printable report; replay canvas of one message spreading; parameter sweep (nodes, range, TTL, buffer, bandwidth) with charts and CSV |
| Shared boards (CRDT, core) | Missing persons, Needs and Resources as paper boards; hybrid logical clocks, an observed-remove set for items and last-writer-wins registers for fields; edits sent as small deltas through store-and-forward and as full states on direct links; deterministic merge; conflict inspector with both values, authors and clocks; a partition test that edits two replicas apart and shows merge(A, B) = merge(B, A) |
| Community SOS | Press-and-hold SOS from every screen; sheet with people, injuries, needs, a self-assessed triage colour, note and location, countdown; top-priority signed message (TTL 72 h, 16 hops) plus an incident on the shared board; reach (phones that stored it and the farthest hop); incoming SOS alert with beep and vibration; responders acknowledge ("I'm coming" with ETA, "On my way"); lifecycle open → acknowledged → en route → resolved as a monotonic register; triage filters (immediate, delayed, minor, deceased) and an incident map; coordinator view with nearest-available suggestions by distance, skills and load; volunteer card (availability, skills); household roll call with last-seen times; banner for your active SOS and for unanswered SOS nearby |
| Identity and security | ECDSA P-256 per device; node id = first 8 bytes of SHA-256 of the compressed key; 33-byte compressed public keys (decompressed with BigInt); every message signed and verified, forged or altered copies rejected; ECDH P-256 → HKDF-SHA-256 (message id as salt) → AES-GCM-256 for direct chats; fingerprint as 8 words that match across English and Urdu; identity QR (base32) to verify a friend in person; trust levels (unverified, signatures seen, verified, blocked); signature tamper demo; key backup |
| Sound link | FSK modem at 100 baud (1600 / 2600 Hz), 24-bit preamble, sync word, length, payload ≤ 64 bytes, CRC-16; continuous-phase WebAudio playback; Goertzel decoder with 1/8-symbol timing search; microphone listening with an 8 s ring buffer; offline loopback with random silence and white noise at a chosen SNR, bit-error count and waterfall; noise sweep chart; decoded frames become (unsigned) #village messages |
| Radio bridge | Web Serial at 115200 baud; DIY line protocol (`TX`, `RX <rssi> <snr>`, `OK`, `PING`) carrying packed signed messages (≤ 240 bytes, key included when it fits); Meshtastic serial frame parser (0x94 0xC3 + 2-byte length, debug text split out); generic protobuf wire-format decoder (varint, zigzag, fixed32/64, float/double, nested messages, strings) as a tree; paste-hex decoder; "request config" frame; simulated board for both modes |
| Offline map | Peers, incidents and responders on a relative grid around you (equirectangular metres, rings, north arrow); distance and bearing list; position update from Geolocation; optional Leaflet street map when online |
| Diagnostics | Bytes and frames per peer, RTT with sparkline, routing counters (stored, duplicates, expired, over hop limit, evicted), frame log with filters, waiting queue, JSON export |
| Platform | Settings (language, dark "field" and light "sand" themes, text size, reduced motion, encryption, receipts, TTL, hop limit, routing strategy, store size, SOS countdown/TTL/hops, demo data, village simulation, tab links, export/import/reset, panic wipe); notifications inbox and activity log; search palette over pages, actions, channels, people, messages, boards, incidents and the roll; 8-step spotlight tour (includes the SOS); keyboard shortcuts; printable situation report and evaluation report; skip link, labels, focus styles, `aria-live` regions, keyboard-closable sheets |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| Codecs | `base32`, `fromBase32`, `b64u`, `crc16`, `fnv1a`, `ByteWriter`, `ByteReader`, `canon`, `sha256` | — |
| Identity and crypto | `newIdentity`, `loadKeys`, `compressPoint`, `decompressPoint`, `idFromPub`, `signMsg`, `verifyMsg`, `sharedSecret`, `dmKey`, `encryptBody`, `decryptBody`, `fingerprintWords` | `rabta.identity` |
| CRDTs | `Hlc.now/recv`, `orAddDelta`, `orRemoveDelta`, `orMerge`, `lwwWrite`, `lwwMerge`, `lwwConcurrent`, `lifecycleWrite`, `boardMerge`, `boardAddDelta`, `boardSetDelta`, `boardLifeDelta`, `boardItems`, `boardDigest` | `rabta.boards`, `rabta.clock`, `rabta.conflicts` |
| Routing | `newNode`, `storeAccept`, `evictOne`, `storePurge`, `bloomFor`, `bloomHas`, `summaryOf`, `peerView`, `ROUTERS`, `planTransfer`, `copiesFor`, `noteSent` | `rabta.store` |
| Simulator | `simMobility`, `simContacts`, `simWorkload`, `simRun`, `simEncounters`, `simPush`, `simSummarise`, `simCompare`, `runSimUI`, `runSweep`, `drawReplay`, `exportSimCsv` | `rabta.sim` |
| Protocol | `makeNodeCtx`, `openSession`, `helloFrame`, `checkHello`, `flushSession`, `onMsgFrame`, `afterStored`, `mergeBoard`, `recordReach`, `publishBoard`, `createMsg`, `pingSession`, `memoryLinks`, `encounter` | — |
| WebRTC | `sdpParse`, `packSdp`, `unpackSdp`, `sdpBuild`, `codeFromDesc`, `descFromCode`, `gatherDone`, `rtcCreateOffer`, `rtcAcceptOffer`, `rtcApplyAnswer`, `relaySignal` (stub) | — |
| Tabs and self-test | `tabsStart`, `tabsRecv`, `tabsConnect`, `openSecondNode`, `loopbackTest`, `lbConnect`, `lbPings`, `lbThroughput`, `lbProtocol` | `rabta.<slot>.*` |
| Demo village | `createVillage`, `loadVillage`, `meetVillager`, `walkVillage`, `villagersMeet`, `villagePost`, `villageRespond`, `villageTick` | `rabta.village` |
| Chats | `renderChats`, `railHtml`, `bubbleHtml`, `locCard`, `openHopSheet`, `sendTo`, `sendText`, `sendLocation`, `sendPhoto`, `startVoice`, `sendSafe`, `sendReceipts`, `decryptPending` | `rabta.store`, `rabta.ui` |
| Community SOS | `bindSosHold`, `openSosSheet`, `sendSos`, `showSosAlert`, `ackIncident`, `resolveIncident`, `reopenIncident`, `setTriage`, `assignResponder`, `suggestResponders`, `reachOf`, `rollTab`, `setRoll`, `volunteerTab` | `rabta.boards` (incidents, roll, responders), `rabta.profile` |
| Boards | `renderBoards`, `paperCard`, `openBoardForm`, `addBoardItem`, `saveBoardFields`, `removeBoardItem`, `conflictsHtml`, `partitionRandom`, `bindPartition` | `rabta.boards`, `rabta.conflicts` |
| Mesh view | `meshGraphSvg`, `meshPanelHtml`, `renderMesh`, `pendingMine`, `storeStatsHtml` | — |
| Sound | `fskFrameBits`, `fskModulate`, `addNoise`, `goertzel`, `fskSoft`, `fskFindSync`, `fskDecode`, `spectrogram`, `runSoundLoopback`, `soundTrials`, `startListen`, `deliverSound` | — |
| Radio and wire formats | `pbDecode`, `pbReadings`, `pbEncode`, `FrameParser`, `mtWrap`, `packMsg`, `unpackMsg`, `parseRadioLine`, `serialConnect`, `radioLine`, `radioSendText`, `simRadioTick`, `describeMeshtastic` (stub) | — |
| Map | `enu`, `gridMapSvg`, `mapPoints`, `renderMap`, `loadLeaflet` | `rabta.profile.pos` |
| Identity view | `identityCode`, `parseIdentityCode`, `verifyScanned`, `renameMe`, `tamperDemo` | `rabta.contacts`, `rabta.profile` |
| Diagnostics, inbox | `onFrame`, `diagPeer`, `renderDiag`, `exportDiag`, `notify`, `logEvent`, `renderInbox` | `rabta.inbox`, `rabta.log` |
| Settings and data | `renderSettings`, `setDemo`, `reseedDemo`, `exportData`, `importData`, `resetDemo`, `wipeDevice` | `rabta.settings`, `rabta.lang` |
| Reports, search, tour | `sitrepHtml`, `evaluationHtml`, `openPalette`, `startTour`, `openShortcuts`, `onKey` | `rabta.settings.tourDone` |
| Shell | `ROUTES`, `route`, `renderRoute`, `renderSoon`, `renderNav`, `renderBanner`, `applyPrefs`, `setLang`, `registerOfflineShell` (stub), `boot` | URL hash |

## Architecture (single file)

```
index.html
├─ <head>      fonts (Geologica, Space Mono, Noto Nastaliq Urdu), favicon, meta
├─ <style>     tokens (charcoal/graphite, hi-vis orange, signal green, sand paper; light "sand and ink"),
│              motion kit, shell (channel-selector nav, peers rail, conversation, mesh panel, phone tabs,
│              floating SOS), chat bubbles, paper boards, triage, maps, charts, connect, sound, radio,
│              sheets, palette, tour, toasts, SOS alert, responsive rules, print
├─ <svg>       117-symbol icon sprite generated from the icons the code uses
├─ shell       nav rail, rail, top bar (packet ticker, link status, search, inbox, theme, language),
│              banner, view, mesh panel, tab bar, overlays
└─ <script>
   ├─ QR encoder      shared kit, unchanged (QR.encode / toSvg / toCanvas)
   ├─ utils           slot-aware store (rabta. prefix), dates, geo maths, toast, motion, modal, files
   ├─ i18n            STR [English, Urdu], t(), L()
   ├─ content         channels, quick messages, board schemas, triage, skills, demo village, scripts
   ├─ engines         byte codecs · identity and crypto · CRDTs · routing · simulator · protocol ·
   │                  WebRTC codes · tabs and loopback · FSK modem · protobuf / Meshtastic / LoRa
   ├─ state + seed    settings, persistence, contacts, conversations, receipts, decryption,
   │                  signed seed messages, seeded boards and incidents, demo village
   ├─ hooks           diagnostics, ticker, arrivals, alerts, conflicts, assignments
   ├─ views           chats + composer · community SOS · boards · charts · mesh + simulator · connect ·
   │                  sound · radio · map · identity · diagnostics + inbox · settings · reports ·
   │                  search, tour, shortcuts · about
   ├─ strings         chat, SOS, mesh/boards, tech, platform dictionaries
   └─ router + boot   ROUTES, nav, banner, renderSoon, applyPrefs, setLang, boot()
```

The numbered parts and `build.sh` that produce this file live in the scratch folder `30-rabta-parts/`.

## Domain logic and algorithm notes

**Connection codes.** After ICE gathering (done on the browser's signal or 0.5 s after the last candidate), the description is packed as: `version|type` (1 byte), setup role (1), ufrag and password (length byte + 6 bits a character), SHA-256 fingerprint (32), candidate count, then per candidate flags (address kind IPv4 / IPv6 / mDNS / name, UDP or TCP and tcptype, candidate type), port (2) and address (4, 16 or 16 bytes), followed by CRC-16/CCITT. The bytes become RFC 4648 base32 behind the prefix `RB`. Base32 (A–Z, 2–7) costs 5.5 QR bits per 5 data bits in alphanumeric mode, survives lower case, spaces and hyphens when pasted, and can be read aloud; base45 would be ≈ 6 % shorter but contains spaces and symbols that chat apps mangle. The receiver rebuilds `v=, o=, m=application … webrtc-datachannel, a=candidate (RFC 5245 priority), a=ice-ufrag/pwd, a=fingerprint, a=setup, a=mid:0, a=sctp-port:5000`.

**Sync protocol.** Frames are JSON. `hello` carries the node id, both public keys, a proof (ECDSA over `rabta-hello|id|ts`), the summary vector and a digest per board; a peer whose id ≠ SHA-256(pk)[0..8] or whose proof fails is dropped. Then each side runs

```
for e in plan(strategy, myStore, peerView, now) sorted by (prio desc, ts asc):
    send {t:'msg', m: e.m, path: e.path, copies}
    peerView.has.add(e.m.id)
on msg: if id known → count duplicate; verify signature; storeAccept(path + [me]);
        apply board deltas; record SOS reach; flush every other open link
```

Boards whose digests differ are exchanged as full states and merged; a side sends its merged state back only if it differs from what it received, so the exchange ends.

**Summary vectors and Bloom filters.** Up to 200 ids are sent as a list; above that a Bloom filter with m = −n·ln p / (ln 2)² bits and k = (m/n)·ln 2 hashes (p = 1 %, double hashing over FNV-1a with a fresh salt per exchange so a false positive is not repeated at the next encounter).

**Routing strategies** (`ROUTERS`, shared by phones and the simulator): *direct* — the source hands its own message only to the destination; *flooding* — push everything not yet pushed to that neighbour, without asking and without TTL (duplicates cost airtime); *epidemic* — send only what the summary vector says is missing, with TTL and hop limit; *binary spray-and-wait* — a message starts with L copies, a holder with n > 1 gives floor(n/2) away, with 1 copy it waits for the destination. Broadcasts behave epidemically. Metrics: delivery ratio = delivered / created, latency = first arrival − creation, overhead = transmissions / delivered. Default scenario: 40 nodes, 1.5 km square, 60 m range, 2 h, 150 messages, TTL 45 min, buffer 30, 10 messages per contact step, seed 2026 — epidemic 85 %, spray 79 %, flooding 50 % (buffer pressure), direct 40 %; overhead 38.9, 9.9, 172.6 and 1.0.

**Hybrid logical clock.** Stamp `lllllllll.cccc.node` (ms and counter in base 36, then node id) so plain string order is causal-then-tie-break order. `now`: l' = max(l, wall), c' = (l' = l) ? c + 1 : 0. `recv(r)`: l' = max(l, r.l, wall); c' = max(c, r.c) + 1 if all three equal, c + 1 if l' = l, r.c + 1 if l' = r.l, else 0. Remote stamps more than an hour ahead of the wall clock are ignored.

**Board CRDT.** A board is `{ items: OR-set, f: { item: { field: register } } }`. The OR-set stores add tags per element and tombstoned tags; an element is present if any add tag is not tombstoned (a concurrent re-add survives a remove). A register is `{ v, t, h, o }`: merge keeps the larger `(o, t)`; `h` lists up to six overwritten stamps, so two writes are *concurrent* (a conflict) when neither stamp is in the other's history. The SOS lifecycle uses `o = epoch.rank`, a max-register: "resolved" can never be undone by a late "acknowledged"; reopening starts a new epoch. Each responder writes only `ack_<node id>` and each phone that stores an SOS writes only `seen_<node id>`, so acknowledgements and reach never conflict. Merge is a join (commutative, associative, idempotent); the behaviour test checks this on 150 randomised three-replica runs.

**Signatures and encryption.** Signed bytes are `JSON.stringify([v, id, kind, ch, from, name, to, ts, exp, hl, prio, body, enc])` — everything a relay must not change; the hop path is per copy. Direct chats: shared = ECDH(my private, their public); key = HKDF-SHA-256(shared, salt = message id, info = "rabta-dm-v1") → AES-GCM-256 with a random 12-byte IV. Fingerprint words: the first 48 bits of the node id as eight 6-bit indices into a 64-word list with the same order in English and Urdu.

**FSK modem.** s(t) = A·sin(φ), φ advancing by 2π·f_bit/fs per sample (continuous phase), 4 ms fades. Goertzel per window of N = fs/baud samples: s_n = x_n + 2cos(2πf/fs)·s_{n−1} − s_{n−2}, power = s₁² + s₂² − 2cos(2πf/fs)·s₁·s₂. Soft decision d = (P₁ − P₀)/(P₁ + P₀) every N/8 samples; the 24-bit pattern (last preamble bits + sync word 0x2DD4) is correlated at every offset and accepted above half the maximum score; then length, payload and CRC-16 are read. The loopback decodes at −6 dB broadband SNR because each symbol integrates 480 samples.

**Wire formats.** Protobuf: tag = varint(field << 3 | wire type); 0 varint (shown as unsigned, zigzag `(n >> 1) ^ −(n & 1)` and bool), 1 fixed 64, 2 length-delimited (tried as a nested message, shown as text when printable UTF-8), 5 fixed 32 (also float). Meshtastic serial: `0x94 0xC3 len_hi len_lo payload`, length ≤ 512, anything else is log text. LoRa packet: `'R' flags kind prio hl hops id(8) from(8) [to(8)] ts(4 s) ttl(2 min) ch name payload sig(64) [pk(33)]` ≤ 240 bytes.

**Coordinator.** score = distance (km) + 1.5 × (required skills the responder lacks) + 1 × (open incidents already assigned); required skills come from the needs (rescue → rescue, medical → medical or first aid, transport → transport) and any injuries (first aid).

### DIY LoRa bridge sketch (ESP32 + SX127x, Sandeep Mistry's LoRa library)

```cpp
// Rabta LoRa bridge: the browser writes "TX <base64url>" lines; the board prints "RX <rssi> <snr> <base64url>".
// Use only the frequency and power your local rules allow (433 MHz shown as an example). Pins: TTGO LoRa32 v1.
#include <SPI.h>
#include <LoRa.h>

const long FREQ = 433175000;
const char *B64 = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_";

int b64Val(char c) {
  const char *p = strchr(B64, c);
  return (p && c) ? p - B64 : -1;
}

int b64uDecode(const String &in, uint8_t *out, int max) {
  uint32_t acc = 0;
  int bits = 0, n = 0;
  for (unsigned i = 0; i < in.length(); i++) {
    int v = b64Val(in[i]);
    if (v < 0) return -1;
    acc = (acc << 6) | v;
    bits += 6;
    if (bits >= 8) {
      bits -= 8;
      if (n >= max) return -1;
      out[n++] = (acc >> bits) & 0xFF;
    }
  }
  return n;
}

String b64uEncode(const uint8_t *in, int n) {
  String s;
  uint32_t acc = 0;
  int bits = 0;
  for (int i = 0; i < n; i++) {
    acc = (acc << 8) | in[i];
    bits += 8;
    while (bits >= 6) {
      bits -= 6;
      s += B64[(acc >> bits) & 63];
    }
  }
  if (bits > 0) s += B64[(acc << (6 - bits)) & 63];
  return s;
}

void setup() {
  Serial.begin(115200);
  LoRa.setPins(18, 14, 26);  // SS, RST, DIO0: change for your board
  if (!LoRa.begin(FREQ)) {
    Serial.println("ERR lora init");
    while (true) delay(1000);
  }
  LoRa.setSpreadingFactor(9);
  LoRa.setSignalBandwidth(125E3);
  LoRa.enableCrc();
  Serial.println("INFO 433.175 MHz SF9 BW125");
}

void loop() {
  if (Serial.available()) {
    String line = Serial.readStringUntil('\n');
    line.trim();
    if (line == "PING") Serial.println("PONG");
    if (line.startsWith("TX ")) {
      uint8_t buf[255];
      int n = b64uDecode(line.substring(3), buf, sizeof buf);
      if (n <= 0) {
        Serial.println("ERR bad base64");
      } else {
        LoRa.beginPacket();
        LoRa.write(buf, n);
        LoRa.endPacket();
        Serial.print("OK TX ");
        Serial.println(n);
      }
    }
  }
  int size = LoRa.parsePacket();
  if (size > 0) {
    uint8_t buf[255];
    int n = 0;
    while (LoRa.available() && n < 255) buf[n++] = LoRa.read();
    Serial.print("RX ");
    Serial.print(LoRa.packetRssi());
    Serial.print(' ');
    Serial.print(LoRa.packetSnr(), 1);
    Serial.print(' ');
    Serial.println(b64uEncode(buf, n));
  }
}
```

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Keep private keys as non-extractable CryptoKeys in IndexedDB, with a passphrase-wrapped backup | Medium | `newIdentity()` |
| Forward secrecy for direct chats (ephemeral keys per session or a Double Ratchet) | Hard | `dmKey()` |
| PRoPHET routing (delivery predictabilities with ageing and transitivity) as a fifth strategy, compared in the simulator | Hard | `ROUTERS` |
| Signalling over the mesh: relay offers and answers through existing links to reach friends of friends | Hard | `relaySignal()` |
| Forward error correction (Hamming or Reed–Solomon with interleaving) for the sound modem | Medium | `fskFrameBits()` |
| Map protobuf fields to Meshtastic FromRadio / MeshPacket / Data and bridge text packets both ways | Medium | `describeMeshtastic()` |
| Service worker cache and manifest for an installable offline app | Easy | `registerOfflineShell()` |

Search the source for `TODO(student)`; the About page lists the same seven with a hint each.

## Data and safety notes

- The village, its people, messages, positions, phone relays and boards are **fictional and illustrative**. Demo identities and messages are generated on your device at first start.
- Rabta is a student template, **not an official warning or rescue system**. It reaches only phones that meet yours. Follow NDMA, PDMA, GBDMA and Rescue 1122 instructions; emergency numbers shown (1122, 15, 115) should be verified locally.
- A valid signature proves who wrote a message, not that it is true. Triage colours entered by the person asking for help are a guess, not a medical assessment.
- Private keys are stored in `localStorage` (readable by anyone with the unlocked phone) and are included in JSON exports. Use *Wipe this device* if a phone is lost; copies already handed to other phones stay there.
- WebRTC codes contain local network addresses (often mDNS names); share them only with people nearby. Channel messages are readable by every phone that carries them; only direct chats are end-to-end encrypted, and without forward secrecy.
- Radio: use only legal frequencies and power for your area. The Meshtastic field numbers in simulated frames are illustrative; verify against the official `meshtastic/protobufs`.
- Browsers pause background pages: relaying works while Rabta is open with the screen on.

## Evaluation ideas for the FYP report

- Reproduce the simulator table for three seeds and report mean ± spread; sweep nodes (10–100), range (30–120 m), TTL and buffer size, and explain where flooding collapses (buffer pressure, bandwidth) and where spray-and-wait matches epidemic at a fraction of the overhead.
- Measure SOS versus normal delivery under tight buffers and bandwidth to show the effect of priority ordering.
- Field test with 3–6 phones on one hotspot: time to pair (QR versus paste), sync time and bytes per encounter from Diagnostics, delivery through a walking relay.
- Sound link: success rate against distance and background noise (fan, rain, crowd) with two real phones; compare with the offline noise sweep.
- CRDT: count conflicts during a role-play with two partitioned groups editing the boards, and survey whether volunteers understood the conflict inspector.
- Usability with Urdu-speaking volunteers: SOS completion time from a locked phone, roll-call accuracy, and comprehension of trust levels and fingerprint words.

### How it was verified

`30-rabta-tests/behaviour.mjs` (Playwright, headless Chromium, no hardware or network) runs 46 checks: seed integrity, ECDSA verification and forgery rejection, point compression, encrypted direct messages, identity codes, CRDT commutativity / associativity / idempotence on randomised operations, deterministic SOS acknowledgement merges and the monotonic lifecycle, HLC behaviour, duplicate refusal, hop limit and TTL, SOS-first ordering and eviction, Bloom filters, the simulator (epidemic ≥ direct on seed 2026, overhead ordering, reproducibility), live encounters with the village, the full SOS flow with a responder, coordinator suggestions, chat and roll call, the FSK loopback at 6, 0 and −6 dB, protobuf / Meshtastic / LoRa packing, the simulated radio, the WebRTC loopback self-test, connection-code CRC, two tab nodes over BroadcastChannel, every route in both languages with no raw string keys, the simulator and partition views, search and the SOS sheet, and the panic wipe — 46 / 46 passing, no console errors.
