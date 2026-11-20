# Frank Epps: portfolio

Live: https://sorry2befrank.github.io/portfolio/

A one-page portfolio for Frank Epps Jr., a systems integrator who builds AI systems and runs [Understudy](https://understudy.consulting) (AI consulting and builds, and AI training for organizations). It is written for technical hiring managers and prospective clients.

## What is on the page

Four tabs, switched in place by a few lines of JavaScript (no routing, no framework):

- **Working System**: the three-part system Frank runs his own work on. An Atlas that maps the operation as a typed graph (56 components in 8 layers) and names where each workflow breaks, a console that is send-incapable by design (checked by a test that scans its runtime source), and an engine that runs bounded work orders overnight.
- **Production Studio**: a multi-model image and video pipeline with an identity check for characters and a bulk asset sorter that publishes to a live site.
- **More Systems**: a multi-tenant agent runtime, Authors Codex (a Next.js and Supabase writing workspace), an app-rescue engagement (client not named), and a list of smaller builds, including Cosmic Atlas, built for a client.
- **About**: career record (U.S. Air Force, Blue Origin, Archer, Foundation) and contact.

Most of these systems live in private repositories. Where a public live link exists, the page links to it.

## How it is built

- `index.html`: the entire site. Inline CSS and JS; the only external request is Google Fonts.
- `img/`: screenshots of the real systems. Private details (signed-in account rows, browser URLs with record IDs, internal directives and work notes) are cropped or blurred in the current screenshots.

Hosted on GitHub Pages from the default branch root. There is no build step.

## Run it locally

```sh
git clone https://github.com/sorry2befrank/portfolio.git
cd portfolio
python -m http.server 8000
# open http://localhost:8000
```

Opening `index.html` directly in a browser also works.

## Checks

There is no automated test suite. Before publishing, the page is rendered with Playwright at 1440 px and 390 px wide to look for layout breaks and horizontal overflow, and its links are checked for HTTP 200.

## Limits

- Several described systems (the Atlas, console and engine, the agent runtime) have no public source. The page shows screenshots, not code.
- Screenshots are point-in-time captures and can lag the systems they show.
