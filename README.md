# 🎯 SentimentCapture

**Find people already talking about your business on Reddit. Turn them into Google reviewers.**

🔗 **[Live demo → sentimentcapture.vercel.app](https://sentimentcapture.vercel.app)**

---

## What it does

You've got happy customers out there saying great things — just not to you, and not on Google. They're on Reddit, in local subreddits, in threads about the best spots in town.

SentimentCapture searches Reddit for genuine comments mentioning your business, filters out the noise (advice-seeking, hearsay, bullet-list roundups), surfaces the ones that read like real testimonials, and generates a short, natural outreach DM you can send directly to that person.

The whole thing runs in the browser. No backend, no database, no login.

---

## How it works

1. Enter your business name + city
2. Hit **Find Testimonials on Reddit**
3. Get a list of pull-quote cards — each one shows the best sentence from the comment, the subreddit it came from, and a sentiment tag
4. Click **Generate Outreach** on any card to get a ready-to-send Reddit DM
5. Add your Google review link and copy the message

The outreach reads naturally because it quotes what the person actually said. If the comment is genuinely positive, the message opens warm. If it's neutral, it's still polite but doesn't oversell.

---

## The stack

This is a single HTML file. That's it.

```
sentimentcapture/
├── index.html       ← the entire app
├── package.json     ← just sets Node version for Vercel
├── vercel.json      ← tells Vercel to serve the static file
└── .gitignore
```

No framework. No build step. No npm install. The only dependencies are loaded via CDN (Tailwind for styling).

Reddit data comes from their public JSON API — `reddit.com/search.json?q=...&type=comment` — fetched client-side directly from the browser. This is intentional: Reddit blocks server/datacenter IPs (including Vercel Edge Functions and Cloudflare Workers), but allows browser requests.

---

## How to steal it

### Fork and deploy in 5 minutes

```bash
# 1. Clone it
git clone https://github.com/Silicon-Valli/sentimentcapture.git
cd sentimentcapture

# 2. Deploy to Vercel (free)
npx vercel
```

That's it. Vercel detects it's a static file and deploys it as-is.

### How to rebuild it from scratch

The core logic is three functions inside `index.html`:

**`scoreSentiment(text)`** — counts positive and negative words, handles negation (`"not great"` → negative). Returns `positive`, `negative`, or `neutral`.

**`extractBestSentence(text, bizName)`** — finds the single best sentence to use as a pull quote. Tries three tiers: (1) a sentence that names the business AND is positive, (2) a sentence near a business mention that's positive, (3) the globally best sentence in short focused comments.

**`buildTemplate(post, biz, reviewUrl)`** — generates the outreach DM. Uses warm language only when sentiment is confirmed positive and a real quote was found. Otherwise falls back to a neutral tone.

Everything else — the search, filtering, UI — is glue code around these three.

### Quality filters worth keeping

The search rejects comments that:
- Are asking for recommendations (not giving them)
- Use hearsay language ("I heard it's great", "supposedly")
- Are bullet-list roundups with 5+ businesses
- Are long (200+ words) but only mention the business once
- Don't produce a pull-quote sentence after extraction

These filters cut the result count significantly but make every result actually usable.

---

## Known limitations

- **Desktop only** — Reddit blocks cross-origin requests from mobile browsers (Safari ITP, iOS Chrome). Works on desktop Chrome/Firefox/Safari.
- **No auth** — uses Reddit's public API, which has rate limits and occasionally returns 0 results on retries. Refreshing usually helps.
- **No AI** — outreach is template-based. The templates are good, but they're not magic.

---

## What v2 could look like

- Reddit OAuth login → bypass the browser fetch restriction → mobile works
- Email/CSV export of all results
- CRM-style tracking (who you've already messaged)
- Multi-platform: Yelp reviews, Google Maps Q&As, TripAdvisor

---

## Vibe coded with Claude

This was built in a single session using [Claude](https://claude.ai) as the engineering team. No code was written by hand. The product decisions, filtering logic, and UX direction came from back-and-forth iteration — describe what's wrong with a result, watch it get fixed, repeat.

If you want to build something similar: start with a single HTML file, use the Reddit public JSON API, and spend most of your time on the quality filters. The search part is easy. Making the results not embarrassing is the actual work.

---

*Live at [sentimentcapture.vercel.app](https://sentimentcapture.vercel.app)*
