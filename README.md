# Puddle — a Swamp testnet

A small, disposable pond for testing [Swamp](https://github.com/swamp-protocol/swamp) clients and tools: five identities with **published private keys**, seventeen signed artifacts, two pollable feeds with history, and real content-addressed CIDs. Clone it, serve it, point your client at it, and you have a working corner of Swamp on your desk — no identities to mint, no feeds to stand up, nothing at stake.

## The testnet convention

Testnets are named after **small and temporary waters** — puddle, billabong, tarn, oxbow, kettle, playa — because that's what they are: shallow, short-lived, safe to splash in, and evaporating without regret. Anyone can build one; this repo is both the first instance and the template. The conventions that make a testnet a testnet:

1. **Published keys.** Every private key is committed, in the open, on purpose (`keys-home/`; see [`NOTICE`](NOTICE)). Testnet identities are worthless by design — anyone can sign as Alice, which is exactly the point. Nothing signed by a testnet key means anything anywhere.
2. **The `Testnet:` marker.** Swamp has no chain-ID; the wire format cannot tell a test post from a real one. So every envelope in a testnet carries `Testnet: yes` — an unknown header to every reader (the must-carry invariant carries it harmlessly), and a filter for any reader that cares. If a testnet post leaks into a real pond, it says what it is on its face, forever, under a signature.
3. **The DID roster.** `world/manifest.json` lists every testnet DID. Real-pond readers can drop posts from rostered DIDs; testnet tooling can insist on them.
   
   **Do not sight testnet posts in published feeds or sightings.** Testnet posts have no distribution of their own — Swamp has no firehose, so a test post reaches real readers only if someone *sights* it into a real stream. Don't be that someone. A `Testnet: yes` post in your published sighting is you vouching, under your permanent signature, for content that anyone can forge with keys published in this repository — you **MUST NOT** do it, and if you do, the record is content-addressed and does not forget: you will be known as noisy, or worse, careless about what you sign. There is no moderator to remove the mistake and no score that decays it away; other publishers marking your feed `negative` is just the pond telling each other what you taught it about your judgment. Splash inside the testnet; sign nothing about it outside.
4. **Real CIDs.** Artifacts are addressed by their actual content hashes (CIDv1 / raw / sha2-256 / base32 — what `ipfs add --cid-version=1 --raw-leaves` produces for single-block files), so clients exercise their real fetch-and-verify paths, and `world/ipfs/` can be pinned to actual IPFS unchanged.

## The cast

Five identities, in the tradition of the spec's own examples: **Alice** (posts and sights, feed served here), **Bob** (field notes, feed served here), **Carol** (essays), **Dana** (bookmarks — her `kind=bookmark; ext=bookmarks/0.1` post is the must-carry exercise), and **Iris** (Alice's agent, posting in her own voice with `Source-Voice:` and `Authored-By:`). Fifteen posts from ~100 to ~1000 words, two sighting streams with `Prev:` history, one extension-kind post.

## Use it

```
scripts/testnet-serve        # http://localhost:2629
```

Then point your client at it:

- **Following list:** `http://localhost:2629/following.md` (Alice's and Bob's feeds)
- **Feeds:** `http://localhost:2629/feeds/{alice,bob}/latest` — signed feed-claims per SPEC §4.10 Feed
- **Gateway:** fetch any post as `http://localhost:2629/ipfs/<cid>`

The committed `world/` works as-is — no toolchain needed. To rebuild (after editing the cast, or with your own base URL):

```
KISS=/path/to/kiss scripts/build-world                       # localhost world
TESTNET_BASE=https://puddle.example.org scripts/build-world  # hosted world
```

Rebuilding is deterministic: fixed dates + Ed25519 give identical bytes and identical CIDs for an unchanged cast.

## Sign as the cast

The keystore is the repo's `keys-home/`:

```
HOME=$PWD/keys-home kiss list
HOME=$PWD/keys-home kiss sign --key <alice-did> your-post.md
```

Go ahead and be Alice. Everyone is Alice. That's the pond.

## Prose convention

For `kind=post` bodies: **write each paragraph as one unwrapped line, with a blank line between paragraphs.** Renderers can't reliably tell a hard-wrapped paragraph from a deliberate line-oriented list, so they preserve your line breaks — meaning hard-wrapped prose renders with ragged mid-sentence breaks on someone else's screen. Deliberate line structure (lists, verse) is yours to keep; prose flows better without it.

The cast follows the convention — except Alice's "Second morning," which is deliberately hard-wrapped and carries an inline note saying so. It's kept as the example of what not to do, so client authors can see how wrapped prose actually renders. A testnet is exactly the place to keep one bad example alive.

## What this is not

Not a conformance suite — fixtures with *expected outcomes* are a different artifact (see the Swamp spec repo's `CONTRIBUTING.md`, "When implementations disagree"). Puddle is a *habitat*: a place for clients to swim, not a judge of their form.

## License

MIT — see [`LICENSE`](LICENSE). The cast's posts are test data; reuse freely.
