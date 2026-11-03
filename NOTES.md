# Clone Notes: Xin Một Bữa Cơm (anxin.party)

## Target Overview
- **Source URL**: `https://anxin.party` (mirrored from `https://anxinonline.pages.dev/`)
- **Author & Copyright**: Chu Anh Phuong (2026)
- **Tagline**: "Ăn xin online. Bạn quét, mình no. Rách nhưng sạch, nghèo nhưng lịch sự."
- **Concept**: A satirical yet earnest Vietnamese digital begging / donation single-page application where visitors can sponsor meals ranging from a 5.000đ iced tea to a 100.000đ full meal, with dynamic bank transfer memos, an itemized receipt of joy, coin-drop animations into a cracked ceramic bowl, and tiered gratitude letters.

---

## Architecture & Asset Strategy

### 1. Typography & Self-Hosting
The upstream project relies on Google Fonts CDN for three typefaces:
- **Baloo 2** (weights 600, 800) – Used for bold hero headlines, price tags, and bank amounts.
- **Be Vietnam Pro** (weights 400, 500, 600, 700) – Clean modern body sans for descriptions, labels, and buttons.
- **Playpen Sans** (weights 500, 700) – Handwritten organic accent font for captions, signatures, and informal notes.

**Localization Strategy**:
- 34 `.woff2` font files covering Vietnamese, Latin-ext, Latin, symbols, and mathematical ranges were downloaded directly to `assets/fonts/`.
- A fully local `assets/fonts/fonts.css` stylesheet was created with relative paths (`./<font-file>.woff2`).
- The external `<link rel="stylesheet" href="https://fonts.googleapis.com/...>` was replaced with `<link rel="stylesheet" href="assets/fonts/fonts.css">`.
- Zero external network requests are made on page load.

### 2. Graphics & Artwork
- **Inline SVG Ceramic Bowl**: Rendered with traditional Vietnamese *men lam* (cobalt blue underglaze), cracked porcelain rim, white rice grains, golden egg yolk, dashed repair stitches, and looping steam paths (`@keyframes steam`).
- **Torn Paper & Tape Aesthetic**: Crafted using high-fidelity CSS polygon clip-paths:
  - `--tear-a`: Irregular ragged top and bottom edge for the cardboard placard and receipt.
  - `--tear-b`: Jagged torn edge for the transfer panel.
  - `--tape`: Serrated adhesive tape ends pinning the placard and section badges.
- **VietQR Code**: Retained as an offline embedded base64 PNG data URI (`data:image/png;base64,...`).
- **Social Sharing**: Social banner saved locally as `assets/og.png` (252 KB).

---

## Privacy & Hygiene Audit
- **Removed Third-Party Trackers**: Upstream contained Cloudflare Web Analytics beacon script:
  ```html
  <script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/..." ...></script>
  ```
  This script was completely purged from `index.html`.
- **Zero Telemetry**: No external analytics, CDNs, pixels, or trackers remain.

---

## Interactive Features & Functional State Machine

1. **Donation Tiers & Custom Amount**:
   - `5.000đ` (Ly trà đá) -> Memo: `MOI MINH LY TRA DA`
   - `20.000đ` (Ổ bánh mì) -> Memo: `MOI MINH O BANH MI`
   - `50.000đ` (Tô phở - default) -> Memo: `MOI MINH TO PHO`
   - `100.000đ` (Bữa cơm đủ đầy) -> Memo: `MOI MINH BUA COM`
   - `Tuỳ tâm` (Custom input, min 1.000đ, step 1.000đ) -> Memo: `MOI MINH AN`
2. **Synchronized Real-Time Updates**:
   - Transfer panel displays formatted currency (`vi-VN`) and uppercase memo.
   - Itemized "Hóa đơn niềm vui" (Receipt of Joy) updates item name, price, and total simultaneously.
   - Ceramic bowl caption announces the selected tier.
   - Coin drop animation drops 1 to 4 golden coins (`₫`) into the bowl with random horizontal jitter and 420-degree rotation.
3. **Clipboard Utility**:
   - Copies Account Number, Raw Amount, or Transfer Memo with one click.
   - Provides temporary visual button feedback ("Đã chép") and animated toast confirmation.
   - Falls back to `document.createRange()` text selection if clipboard permissions are restricted.
4. **Thank-You State ("Mình vừa chuyển xong")**:
   - Unhides the celebratory `#thanks` card with spring easing animation.
   - Selects personalized message and vow based on contribution tier.
   - Triggers a celebratory coin shower and smoothly scrolls into view with accessibility focus management.
5. **FAQ Accordion**:
   - Native `<details>` / `<summary>` elements with stylized +/- indicators and zero-lag CSS transitions.
6. **Accessibility & Reduced Motion**:
   - Honors `@media (prefers-reduced-motion: reduce)` by disabling coin physics, dampening transitions, and using instant scrolling.
   - ARIA live regions on stage captions, transfer panel, and thank-you card.

---

## Open Design Semantic Hooks
All key sections are tagged with `data-od-id`:
- `app-root`: Main wrapper container
- `header-top`: Navigation header and brand badge
- `section-hero`: Top hero section
- `hero-placard`: Cardboard placard with torn paper styling
- `hero-bowl`: Interactive ceramic bowl stage
- `section-menu`: Donation menu and pricing options
- `donation-menu`: Radio button selection grid
- `transfer-panel`: Bank details and QR card
- `thanks-cta`: Completion action trigger
- `thanks-card`: Dynamic personalized gratitude card
- `section-promises`: Ethical begging commitments
- `receipt-card`: Interactive torn-paper receipt of joy
- `section-help`: Alternative support cards (Sharing, Wish Drawer & Wall, Job Referral Drawer)
- `section-faq`: Question and answer accordions
- `page-footer`: Footer and copyright statement

---

## Non-Monetary Support Enhancements (Three Help Channels)
- **1. Chia sẻ trang này (Share)**:
  - Resilient multi-tier copy mechanism (`navigator.clipboard` with `document.execCommand('copy')` fallback).
  - Real-time button text feedback (`✓ Đã chép!`) and toast notifications.
  - Symmetrical button layout: clean single primary yellow button (`#F2C335`, 48px height) matching cards 2 & 3 without sub-row clutter.
- **2. Gửi một lời chúc (Warm Wishes)**:
  - Interactive drawer with pre-made inspirational message chips (`🍜 Bữa ăn ngon miệng`, `💪 Cố lên bạn nhé`, `🍀 Vạn sự bình an`, `✨ Lòng luôn lành lặn`).
  - Personalized name and message inputs with validation.
  - Floating heart and blessing animations (`❤️`, `✨`, `💌`, `💖`) into the stage.
  - Persistent local storage (`anxin_wishes`) with live interactive "Sổ lưu bút ấm lòng" (Wall of Wishes).
- **3. Giới thiệu việc làm (Job Opportunity Referral)**:
  - Interactive job referral drawer with role tags (`🛵 Giao hàng`, `🍳 Phụ bếp`, `📦 Kho vận`, `💻 Việc online`).
  - Form fields for job title, work location, contact details, and requirements.
  - Direct SĐT & Zalo liên hệ: `0385458419` (`0385 458 419`) với nút gọi trực tiếp `tel:0385458419`, link Zalo `https://zalo.me/0385458419` và nút sao chép nhanh.
  - Persistent local storage (`anxin_jobs`) and instant coin celebration.
- **Visual Baseline Symmetry**:
  - All 3 cards use `height: 100%`, flexbox layout with `margin-top: auto` on buttons.
  - Eliminated card rotation transforms (`transform: none`) so all three yellow action buttons sit on the exact same vertical baseline (identical Y coordinate and 48px height).
- **Preview Safety**:
  - Removed all `scrollIntoView` invocations to preserve embedded iframe preview stability.
