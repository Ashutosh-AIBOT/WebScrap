# AmbitionBox Web Scraping Plan

## Objective

Scrape public employee data from AmbitionBox.com (India's largest company review platform covering 100K+ companies) for analysis and research purposes.

---

## Data Categories

### 1. Company Listing Data
| Field | Description |
|-------|-------------|
| `companyName` | Official company name |
| `slug` | URL-friendly identifier (e.g., `tcs`, `infosys`) |
| `rating` | Overall rating (1-5) |
| `reviewCount` | Total employee reviews |
| `salaryCount` | Total salary reports |
| `interviewCount` | Total interview questions |
| `industry` | Industry category |
| `locations` | Office locations |
| `highlyRated` | Top positive tags |
| `criticallyRated` | Top negative tags |
| `logoUrl` | Company logo URL |

### 2. Company Reviews Data
| Field | Description |
|-------|-------------|
| `companyName` | Company name |
| `overallRating` | Review rating (1-5) |
| `subRatings` | Category ratings (work-life, career, etc.) |
| `pros` | Positive feedback |
| `cons` | Negative feedback |
| `jobProfile` | Reviewer's role |
| `jobLocation` | Work location |
| `employmentType` | Full-time/Part-time/Contract |
| `workPolicy` | WFH/Office/Hybrid |
| `verified` | If reviewer is verified employee |
| `publishedAt` | Review date |
| `helpfulCount` | Helpful votes |

### 3. Salary Data
| Field | Description |
|-------|-------------|
| `role` | Job designation |
| `companyName` | Company name |
| `avgSalary` | Average annual CTC (INR) |
| `salaryMin` | Minimum reported |
| `salaryMax` | Maximum reported |
| `experienceRange` | Experience level |
| `location` | Job location |
| `reportsCount` | Number of salary reports |

### 4. Interview Questions Data
| Field | Description |
|-------|-------------|
| `companyName` | Company name |
| `question` | Interview question |
| `experience` | Interviewer experience |
| `role` | Position applied |
| `outcome` | Selected/Rejected |
| `difficulty` | Easy/Medium/Hard |

---

## URL Structure

```
Base: https://www.ambitionbox.com

Company Listing:   /list-of-companies
Company Overview:  /overview/{slug}-overview
Company Reviews:   /reviews/{slug}-reviews
Company Salaries:  /salaries/{slug}-salaries
Company Interviews: /interviews/{slug}-interview-questions
Industry Filter:   /list-of-companies?industry={slug}
Location Filter:   /companies-in-{location}
```

---

## Tech Stack

| Component | Tool | Reason |
|-----------|------|--------|
| Language | Python 3.10+ | Best scraping ecosystem |
| HTTP Client | `httpx` | Async support, modern API |
| HTML Parser | `BeautifulSoup4` | Reliable HTML parsing |
| Browser Automation | `playwright` | For JS-rendered content |
| Data Storage | `pandas` + CSV/JSON | Clean tabular output |
| Rate Limiting | `asyncio` + custom delays | Avoid IP blocks |
| Proxy Rotation | Optional residential proxy | For large-scale scraping |

### Installation
```bash
pip install httpx beautifulsoup4 pandas playwright
playwright install chromium
```

---

## Project Structure

```
WebScrapping/
├── plan.md                  # This file
├── scraper/
│   ├── __init__.py
│   ├── client.py           # HTTP client + rate limiter
│   ├── parsers.py          # HTML parsing functions
│   ├── models.py           # Data models/dataclasses
│   └── utils.py            # Helpers (slugify, clean text)
├── scrapers/
│   ├── __init__.py
│   ├── companies.py        # Company listing scraper
│   ├── reviews.py          # Reviews scraper
│   ├── salaries.py         # Salaries scraper
│   └── interviews.py       # Interviews scraper
├── data/
│   ├── raw/                # Raw scraped data
│   └── processed/          # Cleaned data
├── main.py                 # Entry point
├── config.py               # Settings (delays, retries, limits)
└── requirements.txt
```

---

## Scraping Strategy

### Phase 1: Company Listing (Index)
1. Fetch `/list-of-companies` with pagination
2. Extract all company slugs + metadata
3. Store as `companies_index.csv`
4. Use slug list for subsequent scrapers

### Phase 2: Company Reviews
1. Iterate company slugs from Phase 1
2. Fetch `/reviews/{slug}-reviews` (paginated)
3. Extract review details per company
4. Cap at N reviews per company (configurable)
5. Store as `reviews.csv`

### Phase 3: Salary Data
1. Fetch `/salaries/{slug}-salaries`
2. Extract salary records per role
3. Store as `salaries.csv`

### Phase 4: Interview Questions
1. Fetch `/interviews/{slug}-interview-questions`
2. Extract questions + metadata
3. Store as `interviews.csv`

---

## Anti-Detection Measures

| Measure | Implementation |
|---------|----------------|
| Request Delay | 2-5 seconds between requests (randomized) |
| User-Agent Rotation | Rotate realistic browser UAs |
| Headers | Mimic real browser headers |
| Pagination | Respect page limits, don't skip ahead |
| Error Handling | Backoff on 429/503 responses |
| Session Management | Use cookies from initial page load |

---

## Output Format

### CSV Columns per Dataset

**companies_index.csv**
```
name, slug, rating, review_count, salary_count, industry, locations, highly_rated, critically_rated, overview_url, reviews_url, salaries_url, scraped_at
```

**reviews.csv**
```
company_name, company_slug, overall_rating, pros, cons, job_profile, job_location, employment_type, work_policy, verified, published_at, helpful_count, scraped_at
```

**salaries.csv**
```
company_name, company_slug, role, avg_salary, salary_min, salary_max, experience_range, location, reports_count, scraped_at
```

**interviews.csv**
```
company_name, company_slug, question, experience, role, outcome, difficulty, scraped_at
```

---

## Execution Checklist

- [ ] Set up project structure
- [ ] Implement HTTP client with rate limiting
- [ ] Build company listing scraper
- [ ] Build reviews scraper
- [ ] Build salaries scraper
- [ ] Build interviews scraper
- [ ] Add error handling + retry logic
- [ ] Test with 5 companies (TCS, Infosys, Amazon, Flipkart, Wipro)
- [ ] Scale to top 100 companies
- [ ] Export clean datasets
- [ ] Add logging + progress tracking

---

## Ethical Notes

- Scrape only publicly available data
- Respect `robots.txt` (check before large runs)
- Rate limit requests to avoid server load
- Don't bypass authentication walls
- Use data for research/analysis only
