# Mobile Shop Management System

A browser-based management system for a mobile phone shop, designed around the day-to-day work of selling new and pre-owned phones. It brings stock intake, IMEI-level tracking, supplier balances, checkout, payment recording, receipts, and staff access into one Firebase-backed application.

## Why this project matters

Small phone shops often have to reconcile inventory, customer payments, and supplier obligations across paper records or separate spreadsheets. This project connects those tasks to the same phone and sale records:

- Each device is tracked by its 15-digit IMEI, ownership source, purchase/selling prices, and availability.
- Checkout records who bought the device, how payment was split, and whether the transaction included a trade-in.
- Third-party stock carries supplier details and contributes to an outstanding-balance view, helping the shop distinguish owned stock from consignment stock.
- Staff and admin accounts have different intended responsibilities, while the dashboard and searchable sales history make activity easier to review.

The result is a focused operational tool for improving stock traceability, checkout accuracy, and visibility into daily shop activity. It is a client-side application backed by Firebase; it is not a hosted service or a point-of-sale payment processor.

## Features

- Email/password sign-in with active-account checks.
- Daily dashboard with units sold, cash and UPI collected, revenue, available stock, recent sales, and a seven-day sales chart.
- Phone intake with a single-item form, quantity-based IMEI entry, duplicate-IMEI checks, supplier association, and CSV import with a preview.
- Inventory search and filters for brand, owner (shop or third party), and IMEI, with paginated stock results.
- Checkout with customer details, sale-price adjustment, cash/UPI/card payment splits, UPI QR display, and optional trade-in recording.
- A5 PDF bills containing customer, phone, payment, and exchange details.
- Sales history with free-text search, date filtering, and pagination.
- Supplier records with UPI/contact details, stock counts, buying totals, direct payments, and amount owed.
- Admin staff management for account creation, role assignment, and enabling or disabling accounts.
- Responsive shared layout, navigation, and mobile sidebar behavior.

## Project layout

```text
.
|-- index.html
|-- firebase-config.js
|-- firestore.rules
|-- phones_sample.csv
|-- css/
|   |-- main.css
|   `-- pages.css
|-- js/
|   |-- add-phone.js
|   |-- auth.js
|   |-- dashboard.js
|   |-- pdf-generator.js
|   |-- sales-history.js
|   |-- sales.js
|   |-- sidebar.js
|   |-- staff.js
|   |-- third-person.js
|   |-- utils.js
|   `-- view-stock.js
`-- pages/
    |-- add-phone.html
    |-- dashboard.html
    |-- sales-history.html
    |-- sales.html
    |-- staff-management.html
    |-- third-person.html
    `-- view-stock.html
```

### What each file is for

#### Root files

- `index.html` — Login screen, Firebase email/password authentication, active-user validation, and redirect to the dashboard.
- `firebase-config.js` — Initializes the Firebase app, Authentication, and Firestore clients. Update this file when connecting the app to a different Firebase project.
- `firestore.rules` — Starter Firestore rules for the `users`, `phones`, `thirdPersons`, `sales`, `settings`, and `counters` collections.
- `phones_sample.csv` — Example phone-import spreadsheet, including both shop-owned and third-party phones and the CSV column names expected by the intake page.
- `README.md` — Project overview, setup, feature guide, and file map.

#### Styles

- `css/main.css` — Shared visual system: login and form styles, navigation, cards, tables, badges, filters, modals, notifications, payment/QR sections, and responsive layouts.
- `css/pages.css` — Currently empty; reserved for page-specific styles.

#### Shared JavaScript

- `js/auth.js` — Authentication guard, admin-role guard, current-user session helper, and logout behavior.
- `js/sidebar.js` — Shared navigation setup, user identity display, admin-only navigation visibility, active-page state, and mobile sidebar controls.
- `js/utils.js` — Shared toast/loading UI, INR/date formatting, modal helpers, and a bill-number helper.
- `js/pdf-generator.js` — Reusable A5 sale-receipt PDF generator. The checkout page currently contains its own inline PDF-generation implementation.
- `js/view-stock.js` — Loads available phones from Firestore and implements inventory search, brand/ownership filters, and pagination.

#### Page-specific JavaScript placeholders

The following files currently contain no code. Their corresponding pages implement their behavior in inline `<script type="module">` blocks instead:

- `js/add-phone.js`
- `js/dashboard.js`
- `js/sales-history.js`
- `js/sales.js`
- `js/staff.js`
- `js/third-person.js`

#### Application pages

- `pages/dashboard.html` — Daily business summary, recent transactions, available-stock/supplier summaries, and weekly sales chart.
- `pages/add-phone.html` — Phone intake, optional supplier and previous-owner details, quantity-based IMEI inputs, duplicate checks, and CSV import/preview.
- `pages/view-stock.html` — Searchable/filterable view of available phones with ownership and price information; its data logic is in `js/view-stock.js`.
- `pages/sales.html` — Checkout for a selected phone, payment split validation, UPI QR display, trade-in intake, supplier-balance updates, bill numbering, and downloadable PDF receipt.
- `pages/sales-history.html` — Search and date-filtered sale records, including bill/customer/phone/payment/staff details.
- `pages/third-person.html` — Supplier overview and editing for supplier contact/UPI information and amount owed; displays supplier stock and payment totals.
- `pages/staff-management.html` — Admin-only staff list and account creation, role assignment, and active/disabled status controls.

## Requirements

- A modern browser with JavaScript modules enabled.
- A Firebase project with **Email/Password Authentication** and **Cloud Firestore** enabled.
- A local static HTTP server for development. Opening the HTML files directly with `file://` is not supported because the application uses JavaScript modules and Firebase network requests.
- Internet access for Firebase SDK modules and the page libraries loaded from CDNs (Chart.js, jsPDF, QRCode.js, Font Awesome, and Google Fonts).

There is no package manifest or build step in this project.

## Getting started

1. Create or select a Firebase project.
2. Enable **Email/Password** under Firebase Authentication and create a Cloud Firestore database.
3. Replace the Firebase project settings in `firebase-config.js` with the web app configuration from your Firebase project.
4. Publish `firestore.rules` to the Firestore database. Review and adapt the rules to your deployment before using real customer or business data.
5. Create the first administrator:
   - Create the administrator's email/password account in Firebase Authentication.
   - In Firestore, create a `users/{uid}` document whose document ID is that Authentication user's UID. Include at least `displayName`, `email`, `role: "admin"`, and `isActive: true`.
   - This manual bootstrap is needed because staff-account creation is available only to an already-authorized admin.
6. From the project directory, start a local HTTP server, for example:

   ```bash
   python -m http.server 8000
   ```

7. Open `http://localhost:8000/` in a browser and sign in with the administrator account.

The project uses Firebase Web SDK modules from Google-hosted URLs, so a separate `npm install` is not required. Serve the project from the repository root so the relative page and asset paths resolve correctly.

## CSV phone import

Use `phones_sample.csv` as a starting point. The importer requires the following headers (case-insensitive):

```text
Brand,Model,IMEI,PurchasePrice,SellingPrice
```

Optional columns supported by the sample are:

```text
RAM,Storage,Color,Description,IsThirdParty,SupplierName,SupplierUPI,
SupplierPhone,OldOwnerName,OldPurchasePrice,Aadhar
```

The import screen previews the parsed rows and checks required values and existing IMEIs before writing phone and supplier records to Firestore. Keep IMEIs and supplier/customer information private; do not upload real personal data into public sample files or public repositories.

## Firebase data overview

The application reads and writes these Firestore collections:

- `users` — Authentication-linked profile, role, active state, and staff display details.
- `phones` — Device details, prices, IMEI, availability/sold status, ownership source, supplier references, and intake metadata.
- `sales` — Bill number, customer and sold-phone details, payment breakdown, exchange details, and staff attribution.
- `thirdPersons` — Supplier contact/UPI details, phone counts, buying/payment totals, and outstanding balance.
- `settings` — Shop settings, including the UPI details used by checkout.
- `counters` — Bill-number counter.

The Firestore rules express intended role-based access, but they are only a starting point. In particular, thoroughly test authorization paths and constrain which user-profile fields a user may update before production use. Avoid storing Aadhaar numbers or other sensitive identifiers unless they are essential, and apply strict access, retention, and privacy controls if they are stored.

## Deployment

This is a static site and can be served by Firebase Hosting or another static web host configured to serve `index.html` at the site root. Deploy the Firestore rules separately to the same Firebase project. No hosting configuration is included in this repository, so configure the host's public directory and SPA/fallback behavior according to the chosen provider; application pages are separate HTML files linked from the navigation.

## Third-party libraries

- Firebase JavaScript SDK 10.7.1 — Authentication and Firestore.
- Chart.js — Dashboard sales chart.
- jsPDF — Downloadable sales receipt.
- QRCode.js — UPI payment QR code.
- Font Awesome and Google Fonts (Inter) — Icons and typography.

Libraries are loaded from CDNs by the pages at runtime.

## Important limitations

- Firestore access and application configuration must be set up for the Firebase project before the app can be used.
- The app does not process or verify payments; payment amounts are entered and recorded by the operator.
- Checkout writes the sale, stock update, exchange phone, and supplier balance as separate Firestore operations rather than one atomic transaction. Review failure/retry behavior before relying on it for production accounting.
- The security rules, data model, and privacy approach should be reviewed and hardened before using live financial, customer, staff, supplier, or identity data.
