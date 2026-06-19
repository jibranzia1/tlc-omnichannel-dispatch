# TLC Omnichannel Dispatch

A static, browser-based tool that decides where each Shopify order should ship from — the online warehouse or the nearest shop — using daily inventory and order exports. 100% client-side: your files are read in the browser and never uploaded anywhere.

**Live app:** `https://<your-username>.github.io/tlc-omnichannel-dispatch/`

---

## Deploy (one time)

1. Create a new GitHub repo named **`tlc-omnichannel-dispatch`**.
2. Upload **`index.html`** (and this `README.md`) to the repo root.
3. **Settings → Pages → Source: Deploy from a branch → `main` / `root` → Save.**
4. Wait ~1 minute, then open the URL GitHub shows on that page.

## Use (daily / hourly)

1. Open the app URL.
2. Drop your **Store Inventory** export on box 1 and your **Shopify Orders** export on box 2 (`.xlsx` or `.csv`).
3. Check the auto-detected **column mapping & store→city** settings → **Run dispatch analysis**.
4. Filter by date / fulfillment / status / city, then **Export CSV or Excel**.

Column mapping is remembered per browser, so after the first run it's just upload → Run.

---

## Dispatch rules

Matched **SKU first, then design code**:

| Result | Meaning |
|---|---|
| **Can Dispatch (HO / Online)** | Lahore Warehouse (HO) has **> 5** units → ship online. |
| **Dispatch from HO — Low (≤5)** | HO has 1–5 units → still first priority, flagged low. |
| **Ship from Shop — Same City** | HO empty; a shop in the order's city has stock. |
| **Ship from Shop — Other City** | HO empty; routed to the **nearest** city with stock. |
| **Out of Stock — All Locations** | Nothing available anywhere. |

**Warehouse is always first priority.** City nearness: Lahore↔Islamabad↔Karachi (same city first, then nearest).

## Input formats

- **Orders:** standard Shopify orders export (Name, Lineitem sku, Lineitem quantity, Shipping City, Shipping Province, Created at, Lineitem fulfillment status). Multi-item orders are handled — the order's single shipping address is applied to all its line items.
- **Inventory (wide):** `Design, Design code, SKU, Variant, Brand, Subcategory,` one column per store, `Lahore Warehouse (HO), Shopify (PK), Total units, Stock value (MSRP)`. Long layout (row per store) is also supported via the mapping panel.

---

## Notes

- Online-stock threshold is HO **> 5** (`HO_MIN_QTY` in `index.html`).
- The app never substitutes a different size/colour variant; unknown SKUs are flagged out of stock.
- Only external dependency is SheetJS, loaded from a CDN at runtime.
