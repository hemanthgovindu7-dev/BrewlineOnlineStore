# Brewline QA Demo Store
Navigation link: https://hemanthgovindu7-dev.github.io/BrewlineOnlineStore/index.html

A small coffee-gear web store built for practising test automation. There is no backend, no real payments, and nothing leaves the browser, so tests can run as often as you like.

The whole site is a single file: `brewline-store.html`.

## What it does

- **Login and session** — sign in with one of four demo accounts; the session and cart survive a page refresh, but a new tab starts logged out.
- **Product catalog** — eight products across three categories (Gear, Beans, Drinkware), with search, category filter and sorting.
- **Cart** — add/remove items, adjust quantity (1–10), and see a live order summary.
- **Promo codes** — `SAVE10` (10% off) and `FREESHIP` (free shipping).
- **Checkout** — collect customer details, choose card or cash on delivery, and validate every field.
- **Order confirmation** — order ID (format `BL-######`), customer name, email, payment method and total.
- **Admin orders** — `admin_user` additionally sees an Orders list for orders placed in this tab.

Everything is client-side and stored in `sessionStorage`. The **Reset demo data** link in the footer clears the session, cart and orders.

## Running the site

Double-click `brewline-store.html`, or serve it locally:

```bash
python -m http.server 8000
```

Then browse to `http://localhost:8000/brewline-store.html`. Playwright, Selenium and Cypress can open either the file directly or the served URL.

The browser address does not change between views, so tests should wait for a view's `data-testid` to be visible instead of checking the URL.

## Login details

Usernames and passwords are case-sensitive. These are demo credentials only and are also shown on the login page.

| Username        | Password     | Role    | Expected behavior                                      |
| --------------- | ------------ | ------- | ------------------------------------------------------ |
| `standard_user` | `Test@1234`  | Shopper | Logs in and lands on Products                          |
| `admin_user`    | `Admin@1234` | Admin   | Logs in; also sees the Orders menu                     |
| `locked_user`   | `Test@1234`  | Shopper | Login refused: user is locked out                      |
| `slow_user`     | `Test@1234`  | Shopper | Logs in after a 3 second delay (use for wait testing)  |

## Test data

- **Email:** `tester@example.com` (invalid: `tester`, `tester@`, `@example.com`)
- **Postal code:** `12345`, `56001` (invalid: `1234`, `123456`, `560001`, `abcde`)
- **Card (success):** `4111 1111 1111 1111`
- **Card (declined):** `4000 0000 0000 0002` (invalid: `4111 1111`, too short)

## Business rules

Amounts are rounded to the nearest cent, halves rounding up.

| Rule      | Definition                                                                              |
| --------- | --------------------------------------------------------------------------------------- |
| Subtotal  | Sum of unit price × quantity for every cart item                                          |
| Discount  | `SAVE10` gives 10% of the subtotal; otherwise $0.00                                       |
| Shipping  | $5.00 when the subtotal is under $50.00 and `FREESHIP` is not applied; otherwise $0.00   |
| Tax       | 8% of (subtotal − discount)                                                               |
| Total     | Subtotal − discount + shipping + tax                                                      |

### Products

| Product                    | Category  | Price (USD) |
| -------------------------- | --------- | ----------- |
| Gooseneck Kettle 1L        | Gear      | 54.00       |
| Burr Hand Grinder          | Gear      | 79.00       |
| Pour-Over Dripper          | Gear      | 24.00       |
| Digital Coffee Scale       | Gear      | 32.50       |
| Ethiopia Yirgacheffe 250g  | Beans     | 16.00       |
| Colombia Huila 250g        | Beans     | 14.50       |
| Cold Brew Bottle 1L        | Drinkware | 21.00       |
| Ceramic Mug 350ml          | Drinkware | 12.00       |

## Automation hooks

Every control has a unique `id` and a matching `data-testid` with the same value, for example `#login-button` or `[data-testid="login-button"]`. Product elements use a slug in place of `{slug}`: `gooseneck-kettle`, `burr-grinder`, `pour-over-dripper`, `coffee-scale`, `ethiopia-beans`, `colombia-beans`, `cold-brew-bottle`, `ceramic-mug`.

## Out of scope

Real payments, email sending, any server or API; password reset and account registration; product images and detail pages; multiple shipping addresses and saved carts across tabs.

See `Brewline QA Demo Store - Business Requirements Document.docx` for the full requirements, acceptance criteria and locator reference.
