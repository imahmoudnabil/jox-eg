# JOX & MAKER: brief for the new Claude session

Read this file fully before you do anything. The owner speaks simple Egyptian Arabic. Reply in short, simple Egyptian Arabic, with no technical jargon.

## The store
- An EasyOrders store for men's shoes and accessories. Brand name is JOX & MAKER. The design is black and white, with the Cairo font.
- Live domain: https://jox-eg.com (www works too). The old address jox-eg.myeasyorders.com still works.
- This repo (jox-eg) holds two things:
  - **Product photos**, in the category folders (Men_s_Shoes, Sneakers, Wallet, …). The store loads every product photo from
    `https://cdn.jsdelivr.net/gh/imahmoudnabil/jox-eg@main/<path>`. These URLs are saved inside every product **and inside colour-variant swatches**.
  - The team product-editor page lives in a separate repo (jox-products) and runs live at https://marktrack.agency/customer/jox/products/ on Hostinger, saving edits through an `api.php` next to it. Do NOT copy it into this public repo.

## Task 1: move the repo to the enterprise organization, without breaking a single photo
1. Ask the owner **before** the move: will the repo stay **Public** in the new org? jsDelivr can only serve photos from a public repo. If the repo goes private, every product photo on the store disappears.
2. The owner does the move himself (GitHub → Settings → Transfer). Do not do it for him.
3. After the move, change every photo URL from `imahmoudnabil/jox-eg` to `<new-org>/jox-eg`:
   - Product `thumb` and `images`: through the EasyOrders public API (header `Api-Key`, base `https://api.easy-orders.net/api/v1/external-apps`). Before you write anything: GET the product, back it up to a file, then PATCH back the **whole body** with only the image fields changed. Keep the `categories` field as `[{id}]` built from `parsed_categories`, otherwise the product drops out of its category.
   - Colour swatches (variation props) and variant thumbs: the external API does **not** return or edit them. Check whether the new org URL works through jsDelivr. If the old URLs still redirect, leave them. If they don't, tell the owner to fix them from the dashboard, or find another way. Never guess.
   - After the update: open sample products on the live store and confirm every photo loads.
4. If the repo has to be private: keep a separate public repo for the photos, or move the photos to the EasyOrders file server. Discuss it with the owner first.

## Task 2: an image-matching tab inside the marktrack editor page
- Goal: the team picks products that are **the same shoe photographed from another side or another angle**, and saves them as one group from the same link they already use.
- The logic is the same as the matching page we built: https://claude.ai/artifact/54V7BaoAojw8ZynLMiLZWV
  - A grid of products sorted by colour, with a zoom button for each product.
  - Tap the products → «دول منتج واحد» saves a group. Saved groups show a badge, and each group has a delete button.
  - **Another colour of the same model is NOT the same product.** Leave it as it is.
- Save the groups on the same server as the editor (api.php), so the team continues from where they stopped.
- The matching tab lives on the marktrack link only (Hostinger). The temporary claude.ai artifact page is no longer used.
- **Never use GitHub Actions.** The owner does not want to spend Actions minutes. Update the files on Hostinger through File Manager or through Hostinger's own Git (hPanel → Git). Never add `.github/workflows` without asking the owner.
- Before you build: get the real `api.php` from Hostinger, and put the live index.html into this repo first so we have a backup.
- Security: the page password and the write key both sit in the page's client-side code, and the repo is public. Move the password check and the key to the server side (api.php) before you push the editor code.

## Task 3: applying the groups to the store, after the team finishes
For each group:
- The main product is the visible one, or the one with the most photos.
- Add the photos of every other product in the group to the main product, with no duplicates.
- Hide the other products.
- Do not touch colours, sizes or categories.
- Back up every product before you edit it, and check the store after.

## Hard rules that have caused trouble before
- `PATCH /categories/:id` **removes every product from the category**, even if you send `products`. Never PATCH a category. If you have no choice, relink every product to it after the PATCH.
- The `hidden` flag in the products **list** endpoint is wrong. Trust only the single-product GET.
- Every product has `quantity = 0` and stock tracking is off. Real stock lives in the variants (999). Do not hide anything because of quantity.
- The store's head code and CSS (banners, the guarantee block, the photo-reload code) are managed in the other session. **Do not touch them.**
- Pixels: the Meta and TikTok IDs are set in the EasyOrders tracking tools, IDs only. Do not add pixel code, or every order gets counted twice.
- Ask before any destructive or hard-to-undo action.
