# Password Policy & Crack-Time Analysis

A browser-based security research tool that evaluates passwords against common organizational policies, scores their real-world strength, and estimates how long they would take to crack under different attack scenarios. Built as a mini-project for an ethical hacking course.

## Project Description

Most password policies (e.g. "must contain uppercase, lowercase, a digit, and a symbol") are enforced without any evidence that they actually produce secure passwords. This project investigates that gap directly: it takes a dataset of passwords, checks them against two real policy models, scores their actual crackability using a pattern-aware strength estimator, and compares that against a naive brute-force estimate based purely on character-set math.

The core finding the tool is built to demonstrate: **policies that only enforce character complexity can still pass predictable, easily-guessed passwords**, while naive entropy calculations badly overestimate the security of anything that resembles a dictionary word, name, or keyboard pattern. This is the same reasoning behind NIST's 2017 shift away from complexity rules toward length-based policies (NIST Special Publication 800-63B).

The project also includes a companion password generator, so the same page can be used to demonstrate both sides of the problem: how weak passwords get past policy checks, and how a properly random password is constructed.

Everything runs client-side in the browser. No password ever leaves the page or touches a server, which keeps the tool safe to demo with real or sensitive-feeling input during a class presentation.

## Features

**Password dataset analysis**
- Paste any list of passwords (one per line), or load a built-in sample set of widely-published weak passwords plus a few strong ones for contrast
- Test the dataset against two policy models:
  - **Legacy complexity policy** — 8+ characters, requires upper, lower, digit, and symbol
  - **NIST 800-63B style policy** — 12+ characters, must not be a known common password, must not be a single repeated character
- Per-password results table showing length, policy pass/fail, strength score, and crack-time estimates (masked by default, with a reveal toggle)
- Summary statistics: policy pass rate, average strength score, average crack time, weakest password in the set
- Two comparison charts:
  - Strength score distribution across the dataset
  - Naive entropy-based crack time vs. a pattern-aware model, side by side — the core demonstration of why complexity rules alone are insufficient

**Strong password generator**
- Generates passwords using the browser's cryptographic random number generator (`crypto.getRandomValues`), not `Math.random`
- Adjustable length (8–32 characters)
- Optional symbol inclusion and ambiguous-character exclusion (`0/O`, `1/l/I`)
- Guarantees at least one character from each active character class, then shuffles using the same cryptographic RNG so output isn't structurally predictable
- Live strength score and crack-time estimate for each generated password
- One-click copy to clipboard

## How It Works

| Component | Method |
|---|---|
| Policy checks | Regex-based rule matching against each password |
| Naive crack-time estimate | `length × log2(charset size)` used as entropy bits, converted to average guesses needed, divided by an assumed guess rate |
| Pattern-aware strength score | [zxcvbn](https://github.com/dropbox/zxcvbn) — the same library originally built by Dropbox, which accounts for dictionary words, names, dates, keyboard walks, and common substitutions instead of just raw character variety |
| Crack-time scenarios | Offline fast-hash attack (10¹⁰ guesses/sec, e.g. unsalted MD5/SHA1 on modern GPU hardware) and offline slow-hash attack (10⁴ guesses/sec, e.g. bcrypt/scrypt) |
| Password generation | `crypto.getRandomValues` with class-guaranteed characters and a Fisher-Yates shuffle |

## Tech Stack

- Plain HTML, CSS, and JavaScript — no build step, no framework
- [zxcvbn](https://cdnjs.cloudflare.com/ajax/libs/zxcvbn/4.4.2/zxcvbn.js) for pattern-aware password strength scoring
- [Chart.js](https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.9.1/chart.min.js) for the score-distribution and crack-time comparison charts
- Both loaded from cdnjs; everything else runs locally in the browser

## Getting Started

1. Download `password-analyzer.html`
2. Open it directly in any modern browser (Chrome, Firefox, Edge, Safari) — no server or install required
3. Requires an internet connection on first load only, to fetch the zxcvbn and Chart.js libraries from cdnjs

## Usage

1. Paste a list of passwords into the **Dataset** panel, or click **Load sample set**
2. Choose a policy to test against (legacy complexity or NIST-style)
3. Click **Run analysis** to see the summary stats, charts, and per-password breakdown
4. Optionally, use the **Generate a strong password** panel to create a cryptographically random password, adjust its length and character rules, and compare its score against the weak passwords in your dataset

## Data & Ethics Note

The built-in sample dataset draws only on passwords that appear across widely published, publicly known "most common passwords" lists used throughout security research and awareness training (e.g. `123456`, `password`, `qwerty`). No real breach data, scraped credentials, or personal information is included or required to use this tool. All analysis happens entirely in the browser; nothing is transmitted, logged, or stored.

## Possible Extensions

- CSV export of the results table
- Additional policy presets (e.g. PCI DSS, HIPAA-aligned rules)
- Support for uploading a `.txt` wordlist file directly instead of pasting
- A "send generated password into the analyzer" shortcut to test the generator's own output against both policies

## Course Context

Developed as a mini-project for an ethical hacking course, focused on password security, policy evaluation, and the practical difference between compliance-based and evidence-based approaches to authentication security.
