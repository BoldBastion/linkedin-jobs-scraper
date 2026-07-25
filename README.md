[Linkedin Jobs Scraper](https://apify.com/khadinakbar/linkedin-jobs-scraper?fpr=data)

# 🔍 LinkedIn Jobs Scraper — Skills, Salary & B2B Hiring Signals

Extract LinkedIn job listings by keyword + location — no login, no cookies, no manual URL building required. Returns fully structured data including parsed salary (min/max numbers), required skills array, easy-apply flag, applicant count, seniority level, and full job description.

The only LinkedIn jobs actor with a documented output schema — making it the best choice for AI agents (Claude, ChatGPT) and automated pipelines.

Export scraped data, run via API, schedule recurring runs, or integrate with other tools.

---

## Why This Actor?

Most LinkedIn scrapers either require you to manually build search URLs, return raw unstructured text, or cost 5-10x more per result. This actor is different:

- **Simple input** — just type your job title and location. No URL building in incognito windows.
- **Multi-query + multi-location** — run `["Software Engineer", "ML Engineer"]` × `["New York", "London"]` in one run
- **Parsed salary** — returns `salary_min: 180000` and `salary_max: 240000`, not just a raw string
- **Skills as array** — `required_skills: ["Python", "PyTorch"]` for easy filtering
- **B2B hiring signals** — find companies actively hiring to power sales prospecting and competitive intel
- **Built-in deduplication** — cross-query runs never return the same job twice
- **Full output schema** — every field documented for AI agent compatibility

---

## Use Cases

**For recruiters and talent teams:** Aggregate open roles across companies and locations. Monitor competitors' hiring activity. Build sourcing pipelines for specific skill sets.

**For B2B sales teams:** Identify companies hiring for roles that signal budget and buying intent. Find hiring managers and decision-makers. Use job postings as warm outreach triggers.

**For job seekers and market researchers:** Track salary trends across roles and geographies. Analyze which skills are most in-demand. Compare remote vs on-site availability by location.

**For developers and data teams:** Feed structured job data into dashboards, CRMs, ATS systems, or AI pipelines. Schedule daily monitoring runs for specific searches.

---

## Input Parameters

### Simple keyword search (recommended)

```
{
  "searchQueries": ["Software Engineer", "Product Manager"],
  "locations": ["San Francisco, CA", "Remote"],
  "maxResults": 100,
  "datePosted": "r604800"
}
```

This runs 4 searches: each query × each location. Results are automatically deduplicated.

### Direct URL mode (advanced)

Open `linkedin.com/jobs` in an incognito window, apply your filters, copy the URL, and paste it here:

```
{
  "startUrls": [
    { "url": "https://www.linkedin.com/jobs/search/?keywords=data+scientist&location=New+York&f_E=4&f_WT=2" }
  ],
  "maxResults": 200
}
```

### With filters

```
{
  "searchQueries": ["DevOps Engineer"],
  "locations": ["United States"],
  "maxResults": 50,
  "experienceLevel": ["4", "5"],
  "workType": ["2", "3"],
  "jobType": ["F"],
  "salaryBase": "100000"
}
```

---

## Output Fields

Each result is a JSON object with the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `job_id` | string | LinkedIn's unique job ID |
| `job_title` | string | null | Full job title |
| `company_name` | string | null | Hiring company name |
| `company_url` | string | null | LinkedIn company profile URL |
| `company_size` | string | null | Employee count range |
| `company_industry` | string | null | Industry category |
| `location` | string | null | City, state, work arrangement |
| `work_type` | string | null | Remote / Hybrid / On-site |
| `employment_type` | string | null | Full-time / Part-time / Contract |
| `seniority_level` | string | null | Entry / Mid-Senior / Director etc. |
| `salary_range` | string | null | Raw salary string from LinkedIn |
| `salary_min` | number | null | Parsed minimum salary (USD) |
| `salary_max` | number | null | Parsed maximum salary (USD) |
| `required_skills` | string[] | Extracted skills list |
| `is_easy_apply` | boolean | True if LinkedIn Easy Apply is enabled |
| `applicant_count` | string | null | e.g. "142 applicants" |
| `posted_at` | string | null | Relative posting time |
| `job_description` | string | null | Full plain-text description |
| `job_url` | string | Direct link to the job posting |
| `search_query` | string | null | Query that found this job |
| `scraped_at` | string | ISO timestamp of extraction |

### Example output record

```
{
  "job_id": "3812749302",
  "job_title": "Senior Machine Learning Engineer",
  "company_name": "Anthropic",
  "company_url": "https://www.linkedin.com/company/anthropic",
  "company_size": "201-500 employees",
  "company_industry": "Artificial Intelligence",
  "location": "San Francisco, CA (Hybrid)",
  "work_type": "Hybrid",
  "employment_type": "Full-time",
  "seniority_level": "Mid-Senior level",
  "salary_range": "$200,000–$280,000/yr",
  "salary_min": 200000,
  "salary_max": 280000,
  "required_skills": ["Python", "PyTorch", "RLHF", "Distributed Systems"],
  "is_easy_apply": false,
  "applicant_count": "142 applicants",
  "posted_at": "2 days ago",
  "job_description": "We are looking for a Senior ML Engineer...",
  "job_url": "https://www.linkedin.com/jobs/view/3812749302/",
  "search_query": "Machine Learning Engineer",
  "source_url": "https://www.linkedin.com/jobs/view/3812749302/",
  "scraped_at": "2026-03-31T14:22:00.000Z"
}
```

---

## Cost & Performance

**Pricing:** $0.0005 per job result (PAY_PER_EVENT). No actor start fee.

| Scenario | Jobs | Estimated Cost |
| --- | --- | --- |
| Quick search | 50 | ~$0.025 |
| Standard batch | 200 | ~$0.10 |
| Large scrape | 1,000 | ~$0.50 |
| Daily monitoring (50/day) | 1,500/month | ~$0.75/month |

Compare: the dominant competitor charges $0.001/result (2x more). Premium competitors charge up to $0.005/result (10x more).

**Performance:** ~25–50 jobs/minute depending on LinkedIn's response times and proxy routing. 50 jobs typically takes 2–4 minutes.

---

## Filters Reference

| Filter | Values |
| --- | --- |
| `datePosted` | `r86400` (24h), `r604800` (week), `r2592000` (month) |
| `jobType` | `F` (Full-time), `P` (Part-time), `C` (Contract), `T` (Temporary), `I` (Internship) |
| `experienceLevel` | `1` (Intern), `2` (Entry), `3` (Associate), `4` (Mid-Senior), `5` (Director), `6` (Executive) |
| `workType` | `1` (On-site), `2` (Remote), `3` (Hybrid) |
| `salaryBase` | `40000`, `60000`, `80000`, `100000`, `120000` |

---

## Limitations

- LinkedIn limits each search URL to ~1,000 results. Use multiple search queries to get more.
- Salary data is only available on job postings where the employer has added it (typically 30–40% of postings in the US).
- Skills extraction requires LinkedIn to expose the skills section on the public job page. For some postings, skills will be empty.
- LinkedIn may show an auth wall on some requests. The actor retries with a fresh session when this happens.

---

## No Login Required

This actor operates entirely on LinkedIn's public job search pages, which are accessible without an account. No cookies, no session tokens, no LinkedIn credentials needed.

---

## Support & Feedback

Found a bug or want a new feature? Open an issue on this actor's page. We respond within 48 hours and typically ship fixes within a week.

Works great with our [Google Jobs Scraper](https://apify.com/khadinakbar/google-jobs-scraper) for cross-platform job market research.

---

## Changelog

**v0.1** — Initial release. Keyword + location search, multi-query cross-product, full detail page extraction, salary parsing, skills extraction, PPE pricing.