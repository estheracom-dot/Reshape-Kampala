# Reshape-Kampala
Reshape Kampala is a dynamic, fully functional e-commerce storefront designed for selling upcycled glass jars.
A dynamic, multi-language storefront proof of concept (PoC) for selling upcycled glass jars in Kampala
This project is built to prove core mechanics—specifically live stock tracking and complex localization rules—without faking functionality. It relies on forced dynamic rendering to ensure that inventory counts and basket totals are never frozen in static HTML.

Tech Stack
Framework: Next.js (App Router, forced dynamic rendering)

Database: PostgreSQL 16 (Local/Dev) / 18 (Production)

ORM: Prisma

Storage: Amazon S3 (Read-only for PoC)

Core Features
Live Inventory & Basket: Stock is the product. The basket relies on cookie identification to write real rows to the database and hard-caps quantities based on actual available stock.

Complex Internationalization (i18n):

Supports English (root), Luganda (/lg), Kiswahili (/sw), and French (/fr).

Per-field fallback: If a specific translation is missing in the database (e.g., a Kiswahili product description), the UI seamlessly falls back to English for that specific field rather than breaking the page or showing blank space.

Universal search matches names across all languages simultaneously.

The Shelf: Features a dense, price-forward layout with filter chips (size, kind, engraved, price) and provenance details (which bar/hotel the original bottle came from).

Project State & Deliberate Exclusions
As a PoC, several features are explicitly excluded for this phase (see docs/STATE.md for full details):

Checkout & Payment: There is no "dead" checkout button. Instead, the basket hands the order over to WhatsApp to bridge the gap to the shop's existing sales channel until a payment gateway (e.g., Pesapal) is integrated.

Admin Dashboard: Not built. The catalogue is currently populated using a local JSON seed file (content/catalogue.json).

Image Management: S3 is wired for reading images (imageKey resolves through S3_PREFIX), but upload functionality is not built. Unphotographed pieces are currently drawn/rendered.

Email: Resend integration is not yet wired.

Local Development
Prerequisites
Node.js

Docker (for local PostgreSQL instance)

Setup Instructions
Clone the repository and install dependencies:

Bash
npm install
Start the local database:
Ensure Docker is running, then spin up the PostgreSQL 16 container.

Run Prisma migrations and seed the database:
This will populate the database with the draft strings and placeholders from content/catalogue.json.

Bash
npx prisma migrate dev
npm run seed
Start the development server:

Bash
npm run dev
Testing
This project strictly avoids mocks for database interactions. The test suite runs against a live PostgreSQL database to ensure data integrity.

Run the test suite:

Bash
npm run test
Current coverage: 32 tests passing (catalogue filters, search, per-field fallback, Decimal handling, basket stock caps).

reshape-kampala/
├── app/
│   ├── [lang]/                     # Dynamic routing for English, Luganda, Kiswahili, French
│   │   ├── jars/
│   │   │   └── [slug]/
│   │   │       └── page.tsx        # Product page (shows provenance line, forced dynamic)
│   │   ├── layout.tsx              # Root layout carrying the WhatsApp basket & language switcher
│   │   └── page.tsx                # The Shelf (dense, price-forward UI, forced dynamic)
│   └── api/
│       └── health/route.ts         # Live health check endpoint querying Postgres
├── components/
│   ├── basket/
│   │   └── whatsapp-checkout.tsx   # Formats the basket state and hands off to WhatsApp
│   ├── i18n/
│   │   └── language-switcher.tsx   # The dropdown/sheet UI for switching languages
│   └── shelf/
│       ├── filter-chips.tsx        # Size, kind, engraved, price filters
│       └── product-card.tsx
├── content/
│   └── catalogue.json              # The local seed file with draft strings and placeholders
├── docs/
│   ├── STATE.md                    # Explicitly logs unbuilt features (payment, admin, S3 upload)
│   └── ADR/                        # Architecture Decision Records (e.g., why forced dynamic)
├── lib/
│   ├── i18n/
│   │   ├── fallback-engine.ts      # The logic that serves English if a specific field is blank
│   │   └── dictionaries.ts         # Translation loading
│   ├── prisma.ts                   # Postgres client initialization
│   └── s3.ts                       # Read-only configuration resolving imageKeys to S3_PREFIX
├── prisma/
│   ├── migrations/                 # Postgres 16 database migrations
│   ├── schema.prisma               # Data models (Product, Stock, Translations, Cart)
│   └── seed.ts                     # Hydrates the database using content/catalogue.json
├── tests/
│   ├── e2e/                        # The 32 integration tests (run against live Postgres, zero mocks)
│   │   ├── basket.test.ts          # Verifies hard-capping at physical stock limits
│   │   ├── i18n.test.ts            # Verifies per-field fallback routing
│   │   └── search.test.ts          # Verifies universal search across all 4 languages
├── docker-compose.yml              # Spins up the local Postgres 16 instance for dev/testing
├── .env.example                    # Environment contract (Database URL, S3 Prefix, WhatsApp number)
├── next.config.mjs                 # Next.js config (i18n routing overrides, image domains)
├── package.json
└── README.md
