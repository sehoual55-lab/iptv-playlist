# IPTV Playlist — premium white & red website (dark hero band)

Single-page static website for the **IPTV Playlist** brand. The first screen is a **dark
cinematic hero** — near-black background, red glow, white type — and everything below it is
light: white and soft-grey surfaces, near-black text, vivid red for calls to action. No
framework, no build step, no database — plain HTML, CSS and JavaScript in one self-contained
file. Upload and it runs.

---

## 1. Files

```
index.html                    The whole homepage (HTML + CSS + JS + inline SVG icons)
blog.html                     Blog index
blog-what-is-an-iptv-playlist.html    Article: what an IPTV playlist is
blog-iptv-playlist-setup.html         Article: setup on Smart TV, Fire TV, Android TV
blog-m3u-epg-explained.html           Article: M3U, EPG and portals explained
build_blog.py                 Generator for the blog pages (optional, do not upload)
robots.txt                    Crawler rules
sitemap.xml                   XML sitemap
.htaccess                     HTTPS redirect, gzip, caching, security headers (Apache/cPanel)
assets/og-image.webp          Social sharing image, 1200×630
assets/logo-512.webp          Square logo, 512×512
assets/apple-touch-icon.webp  App icon, 180×180
README.md                     This file
```

## 2. Before you go live — required

Search and replace **`your-domain.com`** with your real domain across `index.html`,
`robots.txt` and `sitemap.xml`. It appears in the canonical tag, the Open Graph tags,
the structured data and the sitemap. Leaving the placeholder will hurt indexing.

Also worth doing:

| What | Where |
|---|---|
| Adjust prices, durations, connections | `PLANS` object in the `<script>` block, **and** the matching pricing cards in the HTML |
| Change the phone number | search for `16728962871` (links) and `+1 672 896 2871` (visible text) — also rebuild `assets/og-image.webp`, the number is drawn into it |
| Change the logo | the `#i-logomark` symbol in `index.html`, the same three paths inline in each blog page, and the three files in `assets/` |
| Add legal pages | Portugal/EU consumer rules generally require imprint, privacy policy, terms and a right-of-withdrawal notice for online sales — ask if you want me to build them |

## 3. Upload

**Namecheap / cPanel or any Apache host:** upload everything into `public_html` so that
`index.html` sits directly in it, with `assets/` beside it. Enable *Show Hidden Files* in the
File Manager settings so you can see and upload `.htaccess`. Run AutoSSL under
*SSL/TLS Status* before relying on the HTTPS redirect.

**Vercel, Netlify, Cloudflare Pages:** drop the folder in as a static site, no build command,
output directory = root. `.htaccess` is ignored there; those platforms handle HTTPS and
compression themselves.

## 4. Sections

Header (sticky, with WhatsApp) · Centred **dark** hero with trust badges and four stat tiles ·
IPTV Playlist (3 cards) · **Why IPTV Playlist** (6 reasons + a fact strip) · Devices (12 brand logos) ·
Pricing (4 plans with a connection counter) · SEO article block with a sticky sidebar ·
FAQ (7 questions) · Contact · Footer · Blog (index + 3 articles) · floating WhatsApp widget.

**There is no checkout form.** Every order goes through WhatsApp.

"Get Started", "View Plans" and "Choose a plan" simply scroll to the pricing section. The four
**"Choose Plan"** buttons then open WhatsApp with the order already written out, for example

> Hello, I'm interested in the Gold plan (15 months +3 free) with 2 connections — total
> 92.48 EUR. Please send me the details.

The brand name is deliberately **not** in the message — nothing in the pre-filled WhatsApp text
says "IPTV". It opens as a neutral enquiry.

The plan name, the term, the number of connections and the total are rebuilt every time the
customer taps &minus; or + on the connection counter, so the message always matches the price on
screen. The link is a real `<a href="https://wa.me/...">`, written into the HTML with the
1-connection price, so it still works if JavaScript never runs — the JS only keeps it in sync.
The message text lives in `waOrderUrl()` in the `<script>` block; the phone number is the
`WA_PHONE` constant right above it.

## 5. Ordering — WhatsApp only, and nothing else

The checkout modal is gone entirely: no order form, no name/email/phone fields, no
payment-method step, no honeypot, and none of the CSS or JavaScript that drove them. Verified —
there is not a single `<form>`, `<input>` or modal left anywhere in the five HTML files.
**Every `tel:` link has also been removed**, on the homepage and on all four blog pages: the hero
contact, the contact box, the footer line, the sidebar and the footer's "Call support" item are
now WhatsApp links. WhatsApp is the only way to reach you from the site. The page is
about 13 KB lighter as a result.

The upside beyond size: you never handle personal data on the website, so there is nothing to
store, secure or explain in a privacy policy, and no PCI-DSS question about card data. The
conversation starts in the customer's own WhatsApp, where they can ask a question before paying
and you can answer as a human.

The trade-off, worth knowing: you no longer get an automatic record of orders. Every order lives
in your WhatsApp thread, so if you later want a log (a spreadsheet, a CRM), it has to be kept by
hand — or the form can be brought back and wired to a Google Sheet.

## 6. Plans, connections and pricing logic

| Plan | Term | Bonus | Price (1 connection) |
|---|---|---|---|
| Bronze | 12 months | – | €39.99 |
| Gold | 15 months | +3 months free | €49.99 |
| Platinum | 15 months | +3 months free | €59.99 |
| Exclusive | 24 months | +3 months free | €84.99 |

Each card carries a **connection counter** (1–5). The first connection costs the full plan
price, every additional one is **15% cheaper**. The card price recalculates live and the chosen
count is written into the WhatsApp message.

Formula: `total = base × (1 + 0.85 × (connections − 1))`
Example — Gold with 3 connections: 49.99 + 42.49 + 42.49 = **€134.97**

Edit in two places, which must agree:

```js
var PLANS = {
  bronze:    {name:'Bronze',    months:12, bonus:0, price:39.99},
  gold:      {name:'Gold',      months:15, bonus:3, price:49.99},
  platinum:  {name:'Platinum',  months:15, bonus:3, price:59.99},
  exclusive: {name:'Exclusive', months:24, bonus:3, price:84.99}
};
var EXTRA = 0.85;   // discount factor for extra connections
var MAXQ  = 5;      // maximum connections
```

…and the visible `<article class="plan">` cards. The 1-connection price also appears inside each
card's `href="https://wa.me/…"` fallback link, so update that too if you edit prices by hand.
All prices are placeholders, deliberately easy to swap.

## 6b. WhatsApp

Every contact call-to-action points at `https://wa.me/16728962871` with a pre-filled message:
the header pill, the mobile menu, the sidebar of the SEO block, the contact section, the blog
header and the blog CTA band. A floating chat widget sits bottom-right on every page — bubble
with a pulse, panel with avatar, greeting and "Start chat". It opens itself once per session
after 12 seconds (`sessionStorage`), never again unprompted. Closes on X, Escape or a click
outside. While the mobile menu is open the floating bubble hides itself so it cannot cover the
menu's buttons. To change the number, search for `16728962871` in `index.html` — it also appears
as the `WA_PHONE` constant in the script — and in `build_blog.py`.

## 7. SEO

- Title: *IPTV Playlist – Premium IPTV Playlist Service*
- Meta description as specified, canonical, Open Graph and Twitter cards
- One `<h1>`, clean `<h2>` structure per section
- Primary keyword *IPTV playlist* used naturally in the H1, section headings, FAQ answers and
  body copy; secondary phrases (*IPTV M3U playlist*, *IPTV playlist for Smart TV*, *for Fire TV*,
  *for Android TV*, *IPTV playlist setup*, *subscription*, *provider*) appear once or twice each
  where they actually fit a sentence — no stuffing
- Structured data: Organization, WebSite, FAQPage (7 questions eligible for rich results)
- Internal anchor links between every section, sitemap and robots.txt included

After going live: submit the sitemap in Google Search Console and run the Rich Results test.

## 8. Deliberate wording choices

You asked for no unrealistic claims, and the copy follows that. There is no promised uptime, no
channel count, no "guaranteed" anything. Plans state connections and duration only. The
"Why IPTV Playlist" section makes that explicit rather than hiding it — one of the six reasons is
that we do not quote uptime figures or channel counts, and the fact strip says HD and 4K depend
on what the source provides. Saying so plainly tends to build more trust than a number nobody
believes. The footer states that the service is technical in nature, grants no rights to third-party content,
and that customers may only access content they are legally entitled to view. That line protects
you and costs no conversions — I would leave it in.

## 9. About the theme — dark hero, light page

**The hero is the only dark section.** It runs on `#050505` with a red radial glow and a faint
masked grid, the same treatment as the German IPTV site, so the first thing a visitor sees looks
like a streaming service. Everything from "IPTV Playlist Made Simple" downwards is light.

The sticky header adapts: while it sits over the hero it renders dark (`#050505`, white logo,
light nav links, no bottom border); as soon as you scroll past the hero it flips to the white
glass style. That is handled by the `.hdr:not(.is-stuck)` rules — the `is-stuck` class is
already toggled by the existing scroll listener, so nothing extra runs on scroll.

To make the hero light again, delete the `HERO — DARK BAND` block in the `<style>` section and
the `.hdr:not(.is-stuck)` rules under it; the rest of the page is unaffected.

The red was darkened from `#FF2032` to **`#E00A18`** when the background turned white. The
brighter shade only reached about 3.8:1 contrast against white, which fails WCAG AA for normal
text and for white text on a red button. `#E00A18` sits at roughly 5:1, so buttons, prices,
links and the small uppercase labels are all legible and accessible. It still reads as vivid red.
Inside the dark hero the brighter `#FF2032` is used instead — on near-black it has plenty of
contrast and keeps the glow punchy.

Colour tokens live at the top of the `<style>` block:

```css
--bg:#FFFFFF;      --bg-2:#F6F7F9;   --bg-3:#F1F3F6;
--ink:#0B0C0E;     --muted:#565C65;  --dim:#858B94;
--red:#E00A18;     --red-hi:#B80010;
```

Change those six lines and the whole page follows.

## 10. Performance

One HTML file, no JavaScript libraries, no external images on the critical path, all icons inline
as SVG. The only external request is the Inter font from Google Fonts — self-host it
(`@font-face` + WOFF2 in `assets/fonts/`) if you want zero third-party requests and slightly
faster first paint. Without it the page falls back to the system font stack and still looks fine.

## 11. Tested

- No horizontal scrolling at 320 / 360 / 390 / 430 / 540 / 600 / 720 / 768 / 820 / 900 / 1024 /
  1140 / 1180 / 1300 / 1440 / 1600 / 1920 px
- WhatsApp order links: every plan × 1–5 connections produces the right plan name, term,
  connection count and total (Gold 1× €49.99 → 2× €92.48 → 5× €219.96)
- All "Get Started" / "View Plans" / "Choose a plan" buttons land on the pricing section
- FAQ accordion, sticky header, scroll-spy navigation, mobile menu, click-to-call
- No JavaScript errors in the console
- `prefers-reduced-motion` disables the animations

## 12. Changelog of the last round

- **New logo mark — "Signal Play":** a rounded play button broadcasting two signal arcs, replacing
  the old playlist-bars mark. Swapped in `index.html` (the `#i-logomark` symbol) and inline in all
  four blog pages, and `assets/logo-512.webp`, `assets/apple-touch-icon.webp` and
  `assets/og-image.webp` were regenerated to match
- The social sharing image (`og-image.webp`) also carried the old phone number — rebuilt with the
  new logo and **WhatsApp +1 672 896 2871**
- Phone number changed throughout to **+1 672 896 2871** (`wa.me/16728962871`)
- All `tel:` / click-to-call links removed — WhatsApp is now the only contact channel
- Pre-filled WhatsApp messages rewritten so they no longer contain the words "IPTV Playlist":
  plan buttons send *"Hello, I'm interested in the Gold plan (15 months +3 free) with 2
  connections — total 92.48 EUR. Please send me the details."*, general buttons send
  *"Hello, I have a question about your plans."*
- Copy that offered a phone call ("Call us", "Phone & WhatsApp", "give us a call") rewritten
  for WhatsApp; `format-detection` set to `telephone=no`
- Confirmed: no checkout form, no inputs, no modal anywhere

## 12b. Previous round

- New logo mark (playlist bars + play triangle) and a solid black wordmark; regenerated
  `logo-512.webp`, `apple-touch-icon.webp` and the Open Graph image to match
- Checkout modal removed entirely — ordering now runs through WhatsApp
- Pricing "Choose Plan" buttons open WhatsApp with plan, term, connections and total pre-filled
- Player-interface mockup replaced by the **Why IPTV Playlist** section; the nav item that
  pointed at it is now labelled "Why Us" (the anchor is still `#features`, so old links work)
- Unused icons and dead CSS removed — `index.html` is roughly 19 KB smaller than before
