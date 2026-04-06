omega-complete.tar.gz
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