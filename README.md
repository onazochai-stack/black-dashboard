tar -xzf omega-complete.tar.gz
pnpm install
pnpm --filter @workspace/api-server run dev
pnpm --filter @workspace/lead-system run devomega-complete.tar.gz
├── artifacts/api-server/src/          ← All routes + scrapers (Express)
│   ├── app.ts, index.ts
│   ├── routes/ (20 route files)
│   └── scrapers/ (10 scraper modules)
├── artifacts/lead-system/src/         ← Main OMEGA UI (React, 4,366 lines)
│   ├── App.tsx
│   └── components/ (all panels)
├── artifacts/install-america/src/     ← Customer website
├── artifacts/command-center/src/      ← Dashboard app
├── package.json
└── pnpm-workspace.yaml

tar -xzf omega-complete.tar.gz
pnpm install
pnpm --filter @workspace/api-server run dev
pnpm --filter @workspace/lead-system run dev
# lead_engine.py

import requests
from bs4 import BeautifulSoup
import feedparser
import time

KEYWORDS = ["roof repair", "need contractor", "fix my house", "leak", "window replacement"]

results = []

# ---------------------------
# 1. CRAIGSLIST (RSS FEED - SAFE)
# ---------------------------
def scrape_craigslist():
    print("Scanning Craigslist...")
    url = "https://newyork.craigslist.org/search/sss?format=rss"

    feed = feedparser.parse(url)

    for entry in feed.entries:
        for keyword in KEYWORDS:
            if keyword.lower() in entry.title.lower():
                results.append({
                    "source": "Craigslist",
                    "title": entry.title,
                    "link": entry.link
                })

# ---------------------------
# 2. REDDIT (PUBLIC JSON - SAFE)
# ---------------------------
def scrape_reddit():
    print("Scanning Reddit...")
    url = "https://www.reddit.com/r/HomeImprovement/new.json"

    headers = {"User-Agent": "lead-bot"}

    res = requests.get(url, headers=headers)
    data = res.json()

    for post in data["data"]["children"]:
        title = post["data"]["title"]

        for keyword in KEYWORDS:
            if keyword.lower() in title.lower():
                results.append({
                    "source": "Reddit",
                    "title": title,
                    "link": "https://reddit.com" + post["data"]["permalink"]
                })

# ---------------------------
# 3. INDEED (BASIC SCRAPE - LIGHT)
# ---------------------------
def scrape_indeed():
    print("Scanning Indeed...")
    url = "https://www.indeed.com/jobs?q=contractor"

    headers = {"User-Agent": "Mozilla/5.0"}
    res = requests.get(url, headers=headers)

    soup = BeautifulSoup(res.text, "html.parser")

    jobs = soup.select(".jobTitle")

    for job in jobs:
        title = job.get_text()

        for keyword in KEYWORDS:
            if keyword.lower() in title.lower():
                results.append({
                    "source": "Indeed",
                    "title": title,
                    "link": "https://indeed.com"
                })

# ---------------------------
# RUN ALL
# ---------------------------
def run():
    scrape_craigslist()
    scrape_reddit()
    scrape_indeed()

    print("\n--- LEADS FOUND ---")
    for r in results:
        print(f"[{r['source']}] {r['title']} -> {r['link']}")

if __name__ == "__main__":
    run()