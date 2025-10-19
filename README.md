# 🎮 VaultPlay Draw History

**Every draw. Every entry. Every result. Fully transparent.**

This repository contains the complete audit trail for all VaultPlay prize draws. Every competition result is published here automatically so you can verify you got a fair shot.

[![Live Draws](https://img.shields.io/badge/live_draws-view-green)](./live)
[![Test Draws](https://img.shields.io/badge/test_draws-view-blue)](./test)

## 🔍 Why This Exists

When you enter a VaultPlay competition, you deserve to know the draw was fair. This repo proves it:

- ✅ **Every entry is recorded** - including yours
- ✅ **Results can't be changed** - GitHub commits are permanent
- ✅ **Anyone can verify** - you don't have to trust us
- ✅ **The math doesn't lie** - cryptographic hashing determines winners

**No manipulation. No favoritism. Just math.**

---

## 📂 What's In Here

### [`/live`](./live) - Real Competition Draws
This folder contains all **live prize draws** where actual prizes were awarded. Each draw gets:
- ✅ A permanent commit to this repository
- ✅ A GitHub Release with all details
- ✅ A timestamped audit bundle

**Browse by month:** [`live/2025-01`](./live/2025-01) → Find your competition

### [`/test`](./test) - Test Draws
We run test draws regularly to ensure the system works perfectly. These use the **exact same algorithm** as live draws but don't award prizes. This proves we're not hiding anything.

---

## 🎯 How to Verify YOUR Draw

Let's say you entered **"January 2025 Prize Draw"** and want to verify the results.

### Step 1: Find Your Draw

1. Go to [Releases](../../releases) (fastest way)
2. Or browse [`/live/2025-01`](./live/2025-01) by month
3. Click on your competition name
4. Open `draw.json` - this is the complete audit bundle

### Step 2: Check You're In There

Open the `draw.json` file and search (Ctrl+F / Cmd+F) for your **entry code**.

Your entry code was shown when you entered the competition (e.g., `VP-2025-ABC123`).

```json
{
  "entries": {
    "list": [
      {
        "entryCode": "VP-2025-ABC123",  // ← That's you!
        "rank": 42,                      // ← Your finishing position
        "gamertag": "YourGamertag",
        "score": "123456789...",
        "status": "qualified"
      }
    ]
  }
}
```

**What each field means:**
- `entryCode` - Your unique entry ID
- `rank` - Your finishing position (1 = winner, 2 = runner-up, etc.)
- `gamertag` - Your display name (if provided)
- `score` - Your cryptographic score (explained below)
- `status` - Either "qualified" or "disqualified" (with reason)

### Step 3: Understand the Results

The draw works like this:

1. **Public randomness is used** (from drand.love - a global randomness beacon)
2. **Every entry gets a unique score** based on that randomness
3. **Highest score wins** - it's that simple

**The key point:** The randomness is published AFTER entries close, so nobody (not even us) can predict the winner beforehand.

```
Random Number (drand) + Your Entry Code → SHA-256 Hash → Your Score
```

**Example:**
- Random beacon: `abc123def456...`
- Your entry code: `VP-2025-ABC123`
- Your score: SHA-256(`abc123def456...` + `VP-2025-ABC123`) = `987654321...`

The entry with the highest score wins. Simple math. No tricks.

### Step 4: See the Winner

At the top of `draw.json`:

```json
{
  "results": {
    "winner": {
      "rank": 1,
      "entryCode": "VP-2025-XYZ789",
      "gamertag": "WinnerName",
      "score": "999888777..."
    }
  }
}
```

---

## 🔬 How to Verify the Math (Advanced)

Want to double-check the results yourself? Here's how:

### Method 1: Online Tool (Easiest)

1. Open the `draw.json` file
2. Copy the `randomness.value` field (e.g., `abc123def456...`)
3. Go to [SHA-256 Hash Generator](https://emn178.github.io/online-tools/sha256.html)
4. Paste the randomness and compute the hash - this is the **draw seed**
5. Now compute: `SHA-256(draw_seed + your_entry_code)`
6. Compare your result with the `scoreHex` in the audit bundle

If they match, the draw was calculated correctly. ✅

### Method 2: Python Script

```python
import hashlib

# From draw.json
randomness = "abc123def456..."  # randomness.value
your_entry_code = "VP-2025-ABC123"

# Step 1: Calculate draw seed
seed = hashlib.sha256(randomness.encode()).hexdigest()
print(f"Draw seed: {seed}")

# Step 2: Calculate your score
combined = seed + your_entry_code
your_score = hashlib.sha256(combined.encode()).hexdigest()
print(f"Your score: {your_score}")

# Compare with scoreHex in draw.json
```

### Method 3: Command Line (Mac/Linux)

```bash
# Calculate draw seed
echo -n "abc123def456..." | shasum -a 256

# Calculate your score (replace SEED with result from above)
echo -n "SEED_HEREyour_entry_code" | shasum -a 256
```

### Verify Randomness Source

The randomness comes from **drand** (Distributed Randomness Beacon):
- Check `randomness.verificationUrl` in the draw.json
- Visit that URL to see the exact random value used
- drand is run by organizations like Cloudflare, Protocol Labs, and EPFL
- **It's impossible to manipulate** - published after your entries are locked

Example: `https://api.drand.sh/public/5475483`

---

## 🛡️ Security Features

### Why You Can Trust This

1. **Immutable Git History**
   - Once committed to GitHub, records can't be changed
   - Every change is tracked with timestamps
   - Branch protection prevents deletions

2. **Cryptographic Hashing**
   - SHA-256 is the same algorithm securing Bitcoin and the Internet
   - Deterministic: same inputs = same outputs always
   - Impossible to reverse-engineer a winning entry

3. **Public Randomness**
   - Randomness comes from drand.love
   - Published AFTER entries close
   - Verified by multiple independent organizations
   - We can't manipulate it

4. **Open Source Algorithm**
   - [View the complete code](https://github.com/vaultplay-dev/vaultplay-draw-worker)
   - No hidden logic
   - Audited by the community

### What About Disqualifications?

If an entry was disqualified, it's clearly marked:

```json
{
  "entryCode": "VP-2025-BAD999",
  "status": "disqualified",
  "disqualificationReason": "Quiz answered incorrectly",
  "rank": null
}
```

Common reasons:
- Quiz answered incorrectly
- Terms & conditions violation
- Duplicate entry
- Invalid entry data

Disqualified entries are still included in the audit bundle for transparency, but they don't receive prizes.

---

## 📊 Draw Statistics

Each `draw.json` includes stats:

```json
{
  "statistics": {
    "disqualificationReasons": {
      "Quiz answered incorrectly": 15,
      "Duplicate entry": 3
    },
    "locationDistribution": {
      "countries": {
        "GB": 450,
        "US": 200,
        "CA": 100
      }
    }
  }
}
```

---

## ❓ FAQ

### Can VaultPlay change the results after they're published?

**No.** Once committed to GitHub, the results are permanent. Any attempt to change them would be visible in the commit history.

### How do I know the randomness wasn't chosen to favor someone?

The randomness comes from **drand** - a decentralized beacon run by multiple organizations. It's published at fixed intervals (every 30 seconds) and we use whichever round occurs after entries close. We can't control it.

### What if I find an error?

1. Open an [Issue](../../issues/new) on this repository
2. Include the draw ID and specific concern
3. The community can verify independently
4. We'll investigate and respond publicly

### Why are test draws public?

Transparency. We run test draws frequently to ensure the system works correctly. If we hid these, you might wonder what else we're hiding. Nothing is secret.

### Can I verify old draws?

Yes! All draws remain in this repository forever. Browse by date in `/live` or `/test` folders.

### What's a "bundle hash"?

It's a SHA-256 fingerprint of the entire draw.json file. If even a single character changes, the hash changes completely. It's like a tamper-evident seal.

---

## 🔗 Learn More

- **VaultPlay Website:** [vaultplay.co.uk](https://www.vaultplay.co.uk)
- **Draw Worker Code:** [github.com/vaultplay-dev/vaultplay-draw-worker](https://github.com/vaultplay-dev/vaultplay-draw-worker)
- **drand Randomness:** [drand.love](https://drand.love)
- **SHA-256 Algorithm:** [Wikipedia](https://en.wikipedia.org/wiki/SHA-2)

---

## 💬 Questions?

- Browse [Discussions](../../discussions) to see what others are asking
- Check [Issues](../../issues) for known concerns
- Contact: info@vaultplay.co.uk

---

**Last Updated:** October 2025  
**Algorithm Version:** VaultPlay Draw v1.3
