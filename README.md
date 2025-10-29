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

## 🔒 Your Privacy Protected

We take your privacy seriously while maintaining full transparency:

### **Gamertag Display**
- **By default:** Your gamertag is **truncated** in audit bundles (e.g., `Pla...123` instead of `PlayerName123`)
- **Opt-in only:** Full gamertag display requires explicit consent during entry
- **You control it:** Choose how much you want to reveal

### **Data Retention Schedule**
- **Years 0-3:** Draws remain in this **public repository** for verification
- **Years 3-6:** Draws moved to **private archive** (VaultPlay access only for legal/audit compliance)
- **After 6 years:** Draws are **permanently deleted** or further anonymized

**Currently viewing:** Public draws from the last 3 years. Older draws have been archived.

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
        "gamertag": "Pla...123",         // ← Truncated by default (privacy protection)
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
- `gamertag` - Your display name (truncated unless you opted in for full display)
- `score` - Your cryptographic score (explained below)
- `status` - Either "qualified" or "disqualified" (with reason)

**Note on gamertags:** If you see a truncated gamertag like `Pla...123`, this means the entrant chose the default privacy protection. Full gamertags only appear if the entrant explicitly opted in during entry.

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
      "gamertag": "Win...789",  // Truncated unless they opted in
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

## 🛡️ Security & Privacy Features

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

### Privacy Protection Built-In

1. **Gamertag Truncation**
   - Default: Only first and last few characters shown (e.g., `Pla...123`)
   - Reduces re-identification risk
   - You choose whether to reveal your full gamertag

2. **Email Privacy**
   - Your email is **NEVER published** in audit bundles
   - We use HMAC-SHA256 hashing to create a unique, non-reversible identifier
   - Impossible to reverse-engineer your email from the audit data

3. **Regional Privacy**
   - Location data shown as broad regions (e.g., "England - South East")
   - Not specific counties or cities
   - Reduces re-identification risk

4. **Time-Limited Public Exposure**
   - Draws older than 3 years are moved to private archive
   - After 6 years, permanently deleted or further anonymized
   - Balances transparency with privacy rights

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
      "regions": {
        "England - South East": 150,
        "Scotland - Central": 80,
        "Wales - South Wales": 45
      }
    }
  }
}
```

---

## 🗃️ Archived Draws (3+ Years Old)

**Looking for an older draw?**

Draws older than 3 years are no longer publicly visible in this repository. They have been moved to our private archive for legal and audit compliance.

**Why we archive:**
- UK business record-keeping requirements (6 years)
- Data protection compliance (GDPR - right to erasure after retention period)
- Privacy protection (limited public exposure of personal data)

**Need access to an archived draw?**

If you have a legitimate need to access an archived draw (e.g., dispute resolution, legal inquiry, audit verification):
- Email: info@vaultplay.co.uk
- Subject: "Archived Draw Request"
- Include: Competition name, approximate date, reason for request

We'll assess requests on a case-by-case basis and provide access where legally appropriate.

**After 6 years:** Draws are permanently deleted or further anonymized (region data removed, leaving only pseudonymized identifiers). This ensures we don't retain personal data longer than necessary.

---

## ❓ FAQ

### Can VaultPlay change the results after they're published?

**No.** Once committed to GitHub, the results are permanent. Any attempt to change them would be visible in the commit history.

### How do I know the randomness wasn't chosen to favor someone?

The randomness comes from **drand** - a decentralized beacon run by multiple organizations. It's published at fixed intervals (every 30 seconds) and we use whichever round occurs after entries close. We can't control it.

### Why is my gamertag truncated in the audit bundle?

**Privacy protection.** By default, we only show the first and last few characters of your gamertag (e.g., `Pla...123`). This reduces the risk of someone identifying you from the public audit data. If you want your full gamertag displayed, you can opt in during entry.

### Can I request my gamertag be changed in a published audit bundle?

**No.** Audit bundles are immutable once published - this is essential for maintaining trust and proving fairness. However, you control what gamertag appears when you enter. For future competitions:
- Use a pseudonym if you want privacy
- Keep the default truncated display
- Only opt in for full display if you're comfortable being publicly identified

### What happens to my data after 3 years?

Your entry data is moved to a private archive (not publicly visible) but retained for legal compliance. After 6 years total, it's permanently deleted or further anonymized.

### Can I request early deletion of my data?

Yes. Under GDPR, you have the right to request deletion. However:
- Published audit bundles cannot be altered (immutability is essential)
- We may need to retain some data for legal compliance (e.g., winner records for 6 years)
- Future draws can be excluded if you request it before entry

See our [Privacy Policy](https://www.vaultplay.co.uk/privacy-policy) for full details on your data rights.

### What if I find an error?

1. Open an [Issue](../../issues/new) on this repository
2. Include the draw ID and specific concern
3. The community can verify independently
4. We'll investigate and respond publicly

### Why are test draws public?

Transparency. We run test draws frequently to ensure the system works correctly. If we hid these, you might wonder what else we're hiding. Nothing is secret.

### Can I verify old draws?

Yes! All draws from the last 3 years remain in this repository. Browse by date in `/live` or `/test` folders. Draws older than 3 years are archived (see above).

### What's a "bundle hash"?

It's a SHA-256 fingerprint of the entire draw.json file. If even a single character changes, the hash changes completely. It's like a tamper-evident seal.

---

## 🔗 Learn More

- **VaultPlay Website:** [vaultplay.co.uk](https://www.vaultplay.co.uk)
- **Privacy Policy:** [vaultplay.co.uk/privacy-policy](https://www.vaultplay.co.uk/privacy-policy)
- **Terms & Conditions:** [vaultplay.co.uk/terms](https://www.vaultplay.co.uk/terms)
- **Draw Worker Code:** [github.com/vaultplay-dev/vaultplay-draw-worker](https://github.com/vaultplay-dev/vaultplay-draw-worker)
- **drand Randomness:** [drand.love](https://drand.love)
- **SHA-256 Algorithm:** [Wikipedia](https://en.wikipedia.org/wiki/SHA-2)

---

## 💬 Questions?

- Browse [Discussions](../../discussions) to see what others are asking
- Check [Issues](../../issues) for known concerns
- Contact: info@vaultplay.co.uk

---

**Last Updated:** November 2025  
**Algorithm Version:** VaultPlay Draw v1.4  
**Retention Policy:** 3 years public / 3 years private archive / deletion after 6 years
