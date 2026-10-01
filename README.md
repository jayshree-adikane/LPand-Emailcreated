# LP & Email Automation

A Flask tool that turns campaign materials (logo, partner logo, banner, LP content, form fields,
thank-you content and an asset PDF) into:

- **Landing page**: header (logo + partner logo) → banner → content on the left, form on the right → footer
- **Thank-you page**: opens in the same browser after the form is submitted and **downloads the PDF automatically**
- **Email template**: table-based HTML with inline styles (works in Outlook and Gmail), a CTA button that links to the landing page, a footer and an unsubscribe link, plus a plain-text version

The output is a ZIP:

```
<slug>/
  landing-page/  index.html, thank-you.html, assets/ (logo, partner-logo, banner, pdf, style.css, script.js)
  email-template/ email.html, email.txt, images/
```

## Run

```
pip install -r requirements.txt
python app.py
```
Open http://localhost:5050 (change it with `PORT` in `.env`).

- **Dashboard** (`/`): every campaign is saved. Search, sort, edit, duplicate, delete, open the LP or email, and download the full ZIP, the landing-page-only ZIP, the email-only ZIP, or the leads CSV.
- **Builder** (`/builder/<slug>`): after the first **Save & Generate**, every change auto-saves and refreshes the live preview (Ctrl+S also saves).
  - Rich-text editor for the LP body, thank-you message, email body and consent text: font, size, colour, headings, lists, alignment, links, and a **Button** tool (`#form` scrolls to the form)
  - Header & banner layout: solid or transparent header over the banner (for white logos), header colour, sticky header, logo sizes, padding, banner height, headline on the banner, form on the left or right, footer logo style
  - Drag & drop: upload files by dropping them on each box, and drag ⋮⋮ to reorder form fields
  - Each uploaded file has Download / View / Remove links

## Flow
Email CTA → landing page → form submit (POST `/api/submit/<slug>`, saved to `data/campaigns/<slug>/leads.jsonl`)
→ redirect to `thank-you.html` → PDF downloads automatically as the page opens (plus a download button).
Leads can be downloaded as CSV from the preview panel.

## Lead emails
Copy `.env.example` to `.env` and set your SMTP details. Each lead is then emailed to the "Send each new lead to"
address, and you can optionally send the visitor a confirmation that includes the PDF link.

## Hosting / public image links
Email images must use public URLs. When this server is deployed, set **Public base URL** (or `PUBLIC_BASE_URL`)
and click Generate again. The email will then use `https://<host>/c/<slug>/email-template/images/...`, and the CTA
will point to the hosted landing page. If you host the LP files somewhere else, set **CTA URL** to that address.
The form still posts to this server unless you set a custom form action.

## Content formatting
Blank line = paragraph · `- item` = bullet · `## Heading` · `**bold**` · `*italic*` · `[text](https://url)`
