# הוראות הסורק היומי

הקובץ הזה הוא ההוראות שהסורק האוטומטי (Claude) מקבל בכל בוקר ב-7:40 שעון ישראל. אפשר לערוך אותו כדי לשנות מה נסרק ואיך מדרגים, והשינוי ייכנס לתוקף מהסריקה הבאה.

## The business (use for scoring)
Solo founder, just started, no track record as a company. MSc-level data scientist; ran a pilot at the Ministry of Aliyah and Integration (משרד העלייה והקליטה). Can add 2 people immediately. Wants: AI implementation, AI/software development, data analysis, lectures and workshops (for organizations, teachers, students, municipalities). Small tenders with low threshold requirements are the most valuable; big ones are worth flagging with a subcontract/partner strategy.

## Output files (in this repo)
- `site/data/tenders.json`: a JSON array. Each item: id, title, publisher, number, type, category (one of: ai, data, training, education, funding, registry), url (https, the official page), deadline (ISO 8601 with +02:00 in winter / +03:00 in summer), deadlineText (optional, when the exact time is unknown), budget (optional text), source (short Hebrew name of where it was found), score (0-100 int), status ("open" or "closed"), summary, whyFit, risks, nextStep (all Hebrew, 1-3 sentences each, concrete: contact name and email, clarification-question dates, what to check in the booklet), firstSeen (YYYY-MM-DD, never change it for existing items), lastSeen (today).
- id convention: `mr-<publication number>` for mr.gov.il items, otherwise a short stable ascii slug (`edu-<slug>`, `muni-<city>-<num>`).
- Keep every existing item. Set lastSeen=today on items you saw again, set status "closed" when the deadline passed, update details that changed. Never delete items.
- `site/data/meta.json`: {at (now, ISO with Israel offset), newCount (items added today), briefing (Hebrew, 3-6 short paragraphs separated by \n\n: what's new, the 1-2 things to act on today with deadlines, closing-soon warnings, one longer-term tip), sources: [{name, url or null, ok true/false, found}]}.
- `site/data/scans/<YYYY-MM-DD>.json`: {at, newCount, briefing} as a history record.
- Validate both JSON files parse (python3 -m json.tool) before committing.

## Scoring (0-100)
Topic fit 40 (AI implementation/training/dev = 40, data analysis = 30, general training/lectures/enrichment = 20, other = 0-10) + feasible thresholds for a brand-new small supplier 30 (pool/registry or no stated experience = 30, 1-2 projects = 22, unknown = 15, 5+ projects or big turnover = 5) + time left 15 (>14 days 15, 7-14 = 10, 3-7 = 6, <3 = 2) + money/strategic value 15. Be honest; don't inflate.

## Sources to scan every day (WebFetch; if a page fails, fall back to WebSearch and record ok:false)
- Government procurement (מנהל הרכש) search, these exact URLs work with WebFetch: `https://www.mr.gov.il/ilgstorefront/he/search/general-search?text=<urlencoded term>&s=TENDER` with the terms: בינה מלאכותית, AI, הדרכה, הטמעה, סדנאות, הרצאות, פיתוח תוכנה, ניתוח נתונים, דאטה, חדשנות, מאגר מרצים, למידה דיגיטלית. Also the newest-items list `https://www.mr.gov.il/ilgstorefront/he/search/general-search`. Keep only relevant tenders with a future deadline. For each relevant new one fetch `https://mr.gov.il/ilgstorefront/he/p/<publication number>`, and if it scores 50+ also fetch its tender booklet (חוברת המכרז) attachment link from that page for threshold requirements (תנאי סף) and budget.
- Ministry of Education innovation calls: https://edu-tech.education.gov.il/taknot/kol-kore/ (may be blocked; then WebSearch "קול קורא משרד החינוך מינהל חדשנות").
- Gefen / Ministry of Education program pool: WebSearch for a new round of "מאגר ספקי תכניות ומענים" or "גפן הצטרפות ספקים". The previous round was mr.gov.il 4000549730, closed 3.2.2026. If a new round opens, put it first in the briefing.
- Municipalities and regional councils: WebSearch "מכרז סדנאות בינה מלאכותית עירייה", "מאגר מרצים ומנחי סדנאות 2026 מועצה", "מכרז תוכניות העשרה AI בתי ספר עירייה", "הזמנה להציע הצעות הטמעת AI רשות מקומית". Aggregator listing (titles and deadlines only): https://wizbiz.co.il/%D7%9E%D7%9B%D7%A8%D7%96%D7%99-%D7%A9%D7%A8%D7%95%D7%AA%D7%99-%D7%94%D7%93%D7%A8%D7%9B%D7%94-%D7%95%D7%97%D7%99%D7%A0%D7%95%D7%9A/ ; for a promising title, look for the original municipal page via WebSearch.
- Public bodies, universities, colleges, government companies (like the Yad Ben-Zvi "ייעוץ הדרכה והטמעה AI" tender): WebSearch "מכרז הטמעת AI הדרכה", "מכרז ייעוץ בינה מלאכותית חברה ממשלתית", "מכרז אוניברסיטה בינה מלאכותית".
- National AI Directorate and National Digital Agency RFIs (no thresholds, good for a new supplier): covered by the mr.gov.il "בינה מלאכותית" search.
- Israel Innovation Authority calls: https://innovationisrael.org.il/kol_kore/ ; the Ministry of Economy / SBA AI adoption grant (35% refund to SMBs) next round.
Use WebSearch mode "standard" by default; "extended" only when results look thin.

## Rules
- Facts only from what you fetched. If threshold requirements are unknown, say so in `risks`. Never invent contacts or dates.
- Skip irrelevant tenders (construction, medical supplies, etc.) and anything scoring under 25, unless it is a strategic registry/pool.
