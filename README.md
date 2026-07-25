[Linkedin Jobs Scraper](https://apify.com/cryptosignals/linkedin-jobs-scraper?fpr=data)

# LinkedIn Jobs Scraper — Public Job Listings (No Login Required)

Scrape LinkedIn job listings at scale without login credentials or authentication. This actor uses LinkedIn's public guest API to search and extract job postings, returning structured data including job title, company, location, salary, description, and direct apply links.

**2,100+ successful runs** | **99.7% success rate** | **56 active users**

---

## Why Use This Actor?

LinkedIn is the world's largest professional job board with millions of active listings. But LinkedIn's interface makes it difficult to:

- Export job data in bulk
- Compare salaries across roles and locations
- Track job market trends over time
- Build pipelines of job leads for recruiting
- Feed job data into analytics dashboards

This actor solves all of that. You get clean, structured JSON data from LinkedIn's public job listings — no login, no cookies, no accounts to manage.

---

## Key Use Cases

### 🔍 Job Market Research

Track hiring trends across industries. Monitor which skills are in demand, which companies are hiring, and how salaries change by region. Perfect for HR consultants, workforce analysts, and career coaches who need data-driven insights.

### 📊 Recruitment Analytics

Build dashboards showing competitor hiring activity. Know when a competitor posts 50 new engineering roles — that's a signal. Recruiting firms use this to identify clients who are actively scaling.

### 💼 Lead Generation

Every job posting is a buying signal. A company posting for a "DevOps Engineer" probably needs DevOps tools. A company hiring "SDRs" is scaling sales. Feed job listings into your CRM to identify warm leads based on hiring activity.

### 🤖 Automated Job Alerts

Set up scheduled runs to monitor specific keywords and locations. Get notified when new roles matching your criteria appear. Build custom job boards or feeds for niche audiences.

### 📈 Salary Benchmarking

Collect salary data across roles, locations, and experience levels. Build compensation reports for clients or internal planning. Compare your offers against market rates.

### 🎓 Academic Research

Study labor market dynamics, remote work trends, skill demand evolution, or geographic hiring patterns. Export to CSV/Excel for use in research papers and reports.

---

## How It Works

The actor operates in two modes:

### Mode 1: Search Jobs

Search LinkedIn's public job listings by keyword and location. Returns a list of matching jobs with summary data.

### Mode 2: Get Job Details

Fetch the full details of a specific job posting by URL or job ID. Returns the complete job description, requirements, and metadata.

---

## Input Parameters

| Parameter | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `action` | string | Yes | `search` | `"search"` to find jobs by keywords, `"job"` to get full details of a specific job |
| `keywords` | string | No | `"python developer"` | Search terms (e.g., `"data engineer"`, `"marketing manager remote"`) |
| `location` | string | No | `""` | Location filter (e.g., `"United States"`, `"London"`, `"Berlin"`) |
| `maxItems` | integer | No | `5` | Number of results to return (1–100) |
| `url` | string | No | `""` | LinkedIn job URL or job ID, used with `action: "job"` |

### Input Examples

**Search for remote Python jobs in the US (25 results):**

```
{
  "action": "search",
  "keywords": "python developer remote",
  "location": "United States",
  "maxItems": 25
}
```

**Search for marketing roles in London:**

```
{
  "action": "search",
  "keywords": "marketing manager",
  "location": "London",
  "maxItems": 50
}
```

**Get full details of a specific job:**

```
{
  "action": "job",
  "url": "https://www.linkedin.com/jobs/view/1234567890"
}
```

**Broad search for data engineering jobs (max results):**

```
{
  "action": "search",
  "keywords": "data engineer",
  "maxItems": 100
}
```

---

## Sample Output

Each job listing returns a structured JSON object. Here's what a typical result looks like:

```
{
  "title": "Senior Python Developer",
  "company": "TechCorp Inc.",
  "location": "San Francisco, CA (Remote)",
  "salary": "$140,000 - $180,000/yr",
  "postedDate": "2026-04-05",
  "employmentType": "Full-time",
  "experienceLevel": "Mid-Senior level",
  "jobUrl": "https://www.linkedin.com/jobs/view/3876543210",
  "applyUrl": "https://www.linkedin.com/jobs/view/3876543210/apply",
  "companyUrl": "https://www.linkedin.com/company/techcorp",
  "description": "We're looking for a Senior Python Developer to join our platform team. You'll design and build scalable microservices, mentor junior developers, and work closely with product to ship features that serve millions of users.\n\nRequirements:\n- 5+ years Python experience\n- Experience with FastAPI or Django\n- Strong SQL and database design skills\n- AWS or GCP experience\n- Excellent communication skills\n\nBenefits:\n- Fully remote\n- Equity package\n- Unlimited PTO\n- $5,000 annual learning budget",
  "companyLogo": "https://media.licdn.com/dms/image/...",
  "applicationsCount": "127 applicants"
}
```

**Fields returned** (availability depends on what LinkedIn provides for each listing):

- `title` — Job title
- `company` — Company name
- `location` — Job location (may include Remote/Hybrid/On-site)
- `salary` — Salary range (when disclosed by the employer)
- `postedDate` — When the job was posted
- `employmentType` — Full-time, Part-time, Contract, etc.
- `experienceLevel` — Entry, Mid-Senior, Director, etc.
- `jobUrl` — Direct link to the LinkedIn job posting
- `applyUrl` — Direct application link
- `description` — Full job description text
- `companyUrl` — Link to the company's LinkedIn page
- `companyLogo` — URL to the company logo image
- `applicationsCount` — Number of applicants (when available)

---

## Code Examples

### Python — Search for Jobs and Save to CSV

```
from apify_client import ApifyClient
import csv

client = ApifyClient("YOUR_APIFY_API_TOKEN")

run_input = {
    "action": "search",
    "keywords": "data scientist",
    "location": "New York",
    "maxItems": 50,
}

run = client.actor("cryptosignals/linkedin-jobs-scraper").call(run_input=run_input)

# Save results to CSV
items = list(client.dataset(run["defaultDatasetId"]).iterate_items())

with open("linkedin_jobs.csv", "w", newline="", encoding="utf-8") as f:
    if items:
        writer = csv.DictWriter(f, fieldnames=items[0].keys())
        writer.writeheader()
        writer.writerows(items)

print(f"Saved {len(items)} jobs to linkedin_jobs.csv")
```

### Python — Monitor New Jobs Daily

```
from apify_client import ApifyClient
import json

client = ApifyClient("YOUR_APIFY_API_TOKEN")

# Search for recently posted jobs
run_input = {
    "action": "search",
    "keywords": "machine learning engineer",
    "location": "Remote",
    "maxItems": 100,
}

run = client.actor("cryptosignals/linkedin-jobs-scraper").call(run_input=run_input)

items = list(client.dataset(run["defaultDatasetId"]).iterate_items())

# Filter for jobs with salary info
jobs_with_salary = [job for job in items if job.get("salary")]
print(f"Found {len(jobs_with_salary)} jobs with salary data out of {len(items)} total")

for job in jobs_with_salary[:5]:
    print(f"  {job['title']} at {job['company']} — {job['salary']}")
```

### Python — Get Full Details of a Specific Job

```
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_API_TOKEN")

run_input = {
    "action": "job",
    "url": "https://www.linkedin.com/jobs/view/3876543210",
}

run = client.actor("cryptosignals/linkedin-jobs-scraper").call(run_input=run_input)

items = list(client.dataset(run["defaultDatasetId"]).iterate_items())

if items:
    job = items[0]
    print(f"Title: {job['title']}")
    print(f"Company: {job['company']}")
    print(f"Description:\n{job.get('description', 'N/A')}")
```

### Node.js — Search and Process Results

```
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_APIFY_API_TOKEN' });

const input = {
    action: 'search',
    keywords: 'frontend developer react',
    location: 'United States',
    maxItems: 50,
};

const run = await client.actor('cryptosignals/linkedin-jobs-scraper').call(input);

const { items } = await client.dataset(run.defaultDatasetId).listItems();

console.log(`Found ${items.length} jobs`);

// Group by company
const byCompany = {};
for (const job of items) {
    const company = job.company || 'Unknown';
    byCompany[company] = (byCompany[company] || 0) + 1;
}

console.log('\nTop hiring companies:');
Object.entries(byCompany)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 10)
    .forEach(([company, count]) => {
        console.log(`  ${company}: ${count} open positions`);
    });
```

### Node.js — Get Job Details

```
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_APIFY_API_TOKEN' });

const input = {
    action: 'job',
    url: 'https://www.linkedin.com/jobs/view/3876543210',
};

const run = await client.actor('cryptosignals/linkedin-jobs-scraper').call(input);
const { items } = await client.dataset(run.defaultDatasetId).listItems();

if (items.length > 0) {
    const job = items[0];
    console.log(`${job.title} at ${job.company}`);
    console.log(`Location: ${job.location}`);
    console.log(`Salary: ${job.salary || 'Not disclosed'}`);
    console.log(`Apply: ${job.applyUrl}`);
}
```

### Using the Apify API Directly (cURL)

```
# Start a run
curl -X POST \
  "https://api.apify.com/v2/acts/cryptosignals~linkedin-jobs-scraper/runs?token=YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "action": "search",
    "keywords": "product manager",
    "location": "Berlin",
    "maxItems": 25
  }'

# Fetch results (use the defaultDatasetId from the run response)
curl "https://api.apify.com/v2/datasets/DATASET_ID/items?format=json&token=YOUR_TOKEN"
```

---

## Pricing

This actor uses **Pay Per Event (PPE)** pricing — you only pay for what you scrape.

| Event | Price |
| --- | --- |
| **Per result scraped** | **$0.01** |

**Examples:**

- 25 job listings → **$0.25**
- 100 job listings → **$1.00**
- 1,000 job listings (multiple runs) → **$10.00**

**No platform fees.** You only pay for results. If a run returns 0 results, you pay $0.

Free-plan Apify users get **$5/month in free platform credits** which can be used alongside PPE actor costs. That's enough for approximately **500 job listings per month at no cost** — plenty for testing, small projects, or personal job searches.

> **Tip:** Start with `maxItems: 5` to test your search parameters before scaling up. You'll only be charged $0.05 to verify the results match what you need.

---

## Scheduling & Automation

Set up **scheduled runs** on Apify to automatically scrape new job listings on a daily or weekly basis:

1. Go to the actor's page on Apify Console
2. Click **"Schedule"** in the top menu
3. Set your desired frequency (daily, every 6 hours, weekly, etc.)
4. Configure your input parameters
5. Results are automatically saved to your dataset

Combine with **Apify Integrations** to push results directly to:

- Google Sheets
- Slack notifications
- Webhooks (your own API)
- Zapier / Make.com
- Email alerts

---

## Output Formats

Export your results in any format:

| Format | How |
| --- | --- |
| **JSON** | Default. Download from dataset or use API |
| **CSV** | Click "Export" → CSV in Apify Console |
| **Excel** | Click "Export" → Excel in Apify Console |
| **API** | Fetch programmatically via dataset API endpoint |

---

## Frequently Asked Questions

### Do I need a LinkedIn account?

**No.** This actor uses LinkedIn's public guest API. No login, no cookies, no account required. It only accesses publicly available job listings — the same ones visible to anyone browsing LinkedIn without logging in.

### Is this against LinkedIn's terms of service?

This actor accesses only publicly available data through LinkedIn's public endpoints. It does not bypass any authentication or access private data. Public job listings are intentionally made available by employers to reach the widest audience.

### How many jobs can I scrape per run?

Up to **100 jobs per run** using the `maxItems` parameter. For larger volumes, set up multiple runs with different search parameters (different keywords, locations, or date ranges).

### How fresh is the data?

The data is scraped in real-time from LinkedIn when you run the actor. You always get the latest listings. For monitoring new postings, schedule the actor to run daily or every few hours.

### What if my search returns 0 results?

- Check your keyword spelling
- Try broader search terms
- Remove or change the location filter
- LinkedIn may not have listings matching very niche queries
- **You won't be charged for runs that return 0 results**

### Can I filter by remote/hybrid/on-site?

Yes — include terms like "remote" in your keywords. For example: `"software engineer remote"`.

### Can I get salary data?

Salary information is returned **when the employer includes it** in the listing. Not all job postings disclose salary. The actor returns whatever LinkedIn provides.

### How is this different from other LinkedIn scrapers?

- **No login required** — many competitors require LinkedIn credentials (which risk account bans)
- **Public API only** — we don't scrape HTML or use headless browsers, so it's fast and reliable
- **99.7% success rate** — proven across 2,100+ runs
- **Simple pricing** — $0.01 per result, no hidden fees
- **Two modes** — search for jobs OR get full details of a specific listing

### Can I use this for commercial purposes?

Yes. The data is publicly available. You can use it for analytics, lead generation, research, and building products. Always ensure your use case complies with applicable data protection laws in your jurisdiction.

### What happens if LinkedIn changes their API?

We actively maintain this actor. If LinkedIn makes changes, we update the actor to ensure continued functionality. The 99.7% success rate across 2,100+ runs demonstrates our commitment to reliability.

---

## Rate Limits & Best Practices

- **Start small**: Test with `maxItems: 5` before scaling up
- **Be specific**: Precise keywords return better results than broad ones
- **Use location**: Adding a location filter reduces noise and speeds up results
- **Schedule smart**: Daily runs catch most new postings; hourly is rarely needed
- **Combine searches**: Run multiple searches with different keywords to build comprehensive datasets

---

## Support

Having issues or questions? Use the **Issues** tab on this actor's Apify page to report bugs or ask questions. We typically respond within 24 hours.

---

*Built and maintained by [cryptosignals](https://apify.com/cryptosignals). Trusted by 56+ users with 2,100+ successful runs.*