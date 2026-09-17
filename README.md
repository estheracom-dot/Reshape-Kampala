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
