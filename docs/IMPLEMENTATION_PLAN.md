# Implementation Plan: AI-Powered Portfolio CMS (MVP)

**Last Updated**: March 29, 2026  
**Status**: Phase 1 (MVP) - Implementation Started

---

## Executive Summary

A Django-based backend that automates portfolio updates by syncing with GitHub and using Gemini AI to generate compelling project descriptions. Single-user admin dashboard with SQLite database. Preserves existing portfolio UI/UX.

**Timeline**: 3-4 days to deployed MVP  
**Architecture**: Monolithic (Django + SQLite, no microservices)  
**Deployment**: Render.com or Fly.io (free tier)

---

## Phase 1: MVP (Current Focus)

### Core Features

1. **GitHub Sync** (`sync_github_repos` command)
   - Fetch all repositories for a user via GitHub REST API
   - Store in `Project` model with github_id, repo_name, repo_url, description_raw
   - Default new repos to `is_visible=False`
   - Handle pagination and rate limiting

2. **AI Content Generation** (Synchronous)
   - Fetch README from GitHub API
   - Send to Gemini 1.5 Flash with category-specific prompts
   - Store: `ai_title`, `ai_summary`, `key_features[]`, `tech_stack[]`
   - JSON validation + fallback parsing if Gemini fails

3. **Admin Control** (Django Admin)
   - Toggle `is_visible` per project
   - Select `category` (MAJOR or OTHER)
   - Trigger AI generation for individual projects
   - View sync history and timestamps

4. **Public API** (DRF)
   - `GET /api/projects/` → All visible projects
   - `GET /api/projects/major/` → Only MAJOR category
   - `GET /api/projects/other/` → Only OTHER category
   - JSON response for frontend consumption

5. **Public Portfolio Display** (Django Templates + Existing UI)
   - Reuse existing HTML/CSS from current portfolio
   - Replace hardcoded projects with API-driven data
   - Preserve dark mode, neon green accents, "Space Grotesk" font
   - Responsive design (desktop, tablet, mobile)
   - Two sections: Major Projects (full cards) + Other Projects (compact grid)

---

## Database Schema (Phase 1)

### Project Model

```python
from django.db import models

class Project(models.Model):
    # GitHub Mapping
    github_id = models.IntegerField(unique=True)
    repo_name = models.CharField(max_length=255)
    repo_url = models.URLField()
    repo_description = models.TextField(blank=True, null=True)  # Original GitHub description
    
    # Admin Controls
    is_visible = models.BooleanField(default=False)
    CATEGORY_CHOICES = [
        ('MAJOR', 'Major Project'),
        ('OTHER', 'Other / Lab'),
    ]
    category = models.CharField(max_length=10, choices=CATEGORY_CHOICES, default='OTHER')
    
    # AI-Generated Content
    ai_title = models.CharField(max_length=255, blank=True)
    ai_summary = models.TextField(blank=True)
    key_features = models.JSONField(default=list)  # ["Feature 1", "Feature 2", ...]
    tech_stack = models.JSONField(default=list)    # ["Python", "Django", "PostgreSQL", ...]
    
    # Metadata
    last_synced = models.DateTimeField(auto_now=True)
    ai_generated_at = models.DateTimeField(null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        ordering = ['-last_synced']
    
    def __str__(self):
        return self.repo_name
```

---

## Technical Stack (Phase 1)

| Component | Choice | Reason |
|-----------|--------|--------|
| **Backend Framework** | Django 4.2+ | Mature, batteries-included |
| **Database** | SQLite | Zero ops, file-based, sufficient for MVP |
| **API** | Django REST Framework | Standard, with built-in browsable API |
| **GitHub Integration** | GitHub REST API + requests | Simple, no external dependencies |
| **AI Provider** | Gemini 1.5 Flash | Fast, cost-effective (~$0.010 per project) |
| **Task Queue** | None (synchronous) | Keep MVP simple; add Celery in Phase 2 if needed |
| **Frontend** | Django Templates + HTMX | Lightweight, leverage existing CSS |
| **Deployment** | Render.com or Fly.io | Free tier, simple monitoring |

---

## Folder Structure

```
portfolio-cms/
├── manage.py
├── requirements.txt
├── .env (GitHub token, Gemini API key)
├── .gitignore
├── db.sqlite3 (created after migrations)
├── portfolio_cms/
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── __init__.py
├── projects/
│   ├── models.py
│   ├── admin.py
│   ├── serializers.py
│   ├── views.py
│   ├── urls.py
│   ├── tasks.py (sync_github_repos command)
│   ├── management/
│   │   ├── commands/
│   │   │   └── sync_github_repos.py
│   ├── services/
│   │   └── gemini_handler.py (AI content generation)
│   ├── migrations/
│   └── __init__.py
├── ai_service/
│   ├── gemini_handler.py (moved here in refactor)
│   └── __init__.py
├── templates/
│   ├── base.html (shared layout)
│   ├── portfolio/
│   │   ├── index.html (public portfolio with API data)
│   │   └── admin_dashboard.html (admin control panel)
│   └── includes/ (reusable components)
├── static/
│   ├── css/
│   │   ├── styles.css (from existing portfolio)
│   │   └── admin.css (admin UI tweaks)
│   ├── js/
│   │   ├── script.js (from existing portfolio)
│   │   └── admin.js (HTMX interactions)
│   └── images/ (from existing portfolio)
└── docker-compose.yml (optional, for local PostgreSQL later)
```

---

## Implementation Steps

### Step 1: Django Project Setup
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows

# Install dependencies
pip install Django==4.2.10 djangorestframework==3.14.0 python-dotenv requests google-generativeai

# Create Django project
django-admin startproject portfolio_cms .

# Create projects app
python manage.py startapp projects

# Create migrations & DB
python manage.py migrate
```

### Step 2: Configure Environment
Create `.env`:
```
DEBUG=True
SECRET_KEY=your-secret-key-here
GITHUB_TOKEN=ghp_your_github_personal_access_token_here
GEMINI_API_KEY=your-gemini-api-key-here
GITHUB_USERNAME=m0inak057
```

### Step 3: Implement Project Model
File: `projects/models.py` (see schema above)

### Step 4: GitHub Sync Command
File: `projects/management/commands/sync_github_repos.py`
- Fetch repos from `https://api.github.com/users/{github_username}/repos`
- Create/update `Project` records
- Handle rate limiting (60 requests/hour for unauthenticated, 5000 with token)
- Log results

Usage:
```bash
python manage.py sync_github_repos
```

### Step 5: Gemini AI Service
File: `ai_service/gemini_handler.py`
- Class: `GeminiProjectAnalyzer`
- Methods:
  - `analyze_readme(readme_text, category)` → JSON: {ai_title, ai_summary, key_features[], tech_stack[]}
  - `fetch_readme_from_github(repo_url)` → raw README text
- Prompts:
  - **System**: "You are a Senior Technical Recruiter and Developer. Transform GitHub READMEs into compelling portfolio entries using impact-driven language (e.g., 'Architected,' 'Optimized'). Always respond with valid JSON."
  - **Major Project**: "Analyze this README. Provide JSON: {title (refined name), summary (3 paragraphs: problem, solution, impact), features (list of 3-5 technical features), tech_stack (list of technologies)}"
  - **Other Project**: "Analyze this README. Provide JSON: {title, summary (1 sentence), features (list of 2-3 key features), tech_stack (list of 3-5 techs)}"
- Error handling: Retry logic, JSON validation, fallback to basic parsing

### Step 6: Admin Interface
File: `projects/admin.py`
- Register `Project` model with list display: repo_name, category, is_visible, last_synced
- Custom actions:
  - "Generate/Refresh AI Content" button
  - Trigger AI generation and display status

### Step 7: DRF Serializers & Views
Files: `projects/serializers.py`, `projects/views.py`
- `ProjectSerializer`: All fields
- `ProjectViewSet`:
  - `GET /api/projects/` → All visible projects
  - `GET /api/projects/major/` → Major projects only
  - `GET /api/projects/other/` → Other projects only
  - `PATCH /api/projects/{id}/` → Update is_visible, category (admin only)
  - `POST /api/projects/{id}/generate-content/` → Trigger AI generation

### Step 8: Django Templates (Reuse Existing UI)
File: `templates/portfolio/index.html`
- Copy structure from existing portfolio
- Replace hardcoded project cards with loops:
  ```html
  <!-- Major Projects -->
  {% for project in major_projects %}
    <div class="project-card">
      <h3>{{ project.ai_title }}</h3>
      <p>{{ project.ai_summary }}</p>
      <div class="tech-tags">
        {% for tech in project.tech_stack %}
          <span class="tech-tag">{{ tech }}</span>
        {% endfor %}
      </div>
      <a href="{{ project.repo_url }}">View on GitHub</a>
    </div>
  {% endfor %}
  ```
- Preserve CSS: Keep `styles.css` unchanged, only add minimal overrides

### Step 9: Static Files & CSS
- Copy existing `styles.css`, `script.js`, and `images/` into `static/`
- Minimal tweaks: Only if layout changes (unlikely)

### Step 10: URLs & Configuration
Files: `portfolio_cms/urls.py`, `portfolio_cms/settings.py`
- Register `projects` app
- Add REST framework to INSTALLED_APPS
- Configure STATIC_URL, STATIC_ROOT
- Add `.env` loading via python-dotenv

### Step 11: Test Locally
```bash
python manage.py runserver
# Visit http://localhost:8000/
# Admin: http://localhost:8000/admin/
# API: http://localhost:8000/api/projects/
```

### Step 12: Deploy
Deploy to Render.com or Fly.io free tier:
1. Push code to GitHub
2. Connect repo to Render/Fly.io
3. Set environment variables
4. Deploy
5. Run migrations on live database
6. Test endpoints

---

## API Reference (Phase 1)

### List All Visible Projects
```
GET /api/projects/
Response:
{
  "count": 5,
  "results": [
    {
      "id": 1,
      "repo_name": "wikipedia-summarizer",
      "ai_title": "Wikipedia Summarizer",
      "ai_summary": "A full-stack NLP...",
      "key_features": ["Smart search", "Disambiguation handling", "..."],
      "tech_stack": ["Flask", "Python", "NLP"],
      "category": "MAJOR",
      "repo_url": "https://github.com/m0inak057/wikipedia-summarizer",
      "is_visible": true
    }
  ]
}
```

### Filter by Category
```
GET /api/projects/major/  → Only MAJOR projects
GET /api/projects/other/  → Only OTHER projects
```

### Generate AI Content (Admin)
```
POST /api/projects/1/generate-content/
Headers: Authorization: Token <admin_token>
Response:
{
  "status": "success",
  "message": "Content generated successfully",
  "ai_title": "New Title",
  "ai_summary": "...",
  "key_features": [...],
  "tech_stack": [...]
}
```

### Update Project (Admin)
```
PATCH /api/projects/1/
Headers: Authorization: Token <admin_token>
Body:
{
  "is_visible": true,
  "category": "MAJOR"
}
```

---

## Success Criteria (Phase 1 MVP)

- [ ] `python manage.py sync_github_repos` → Creates 15+ project records in DB
- [ ] Admin can toggle `is_visible` for each project
- [ ] Admin can select `category` (MAJOR or OTHER)
- [ ] "Generate Content" button → AI generates summary + tech stack in < 15 seconds
- [ ] `GET /api/projects/` → Returns only visible projects
- [ ] Public portfolio displays Major Projects with full cards + Other Projects compact
- [ ] Existing UI (styles, layout, colors) preserved on live portfolio
- [ ] Deploy to Render.com or Fly.io without errors

---

## Phase 2: Optional Enhancements (Post-MVP)

Only if needed after testing Phase 1:

- Add Celery + Redis for async AI generation (background processing)
- Migrate skills, certifications, education to CMS
- Custom HTMX admin dashboard (replace Django admin)
- Email notifications when content is generated
- GitHub webhook for real-time sync on push
- Analytics (view counts, project clicks)

---

## Key Decisions & Trade-offs

| Decision | Trade-off |
|----------|-----------|
| **SQLite over PostgreSQL** | Simpler setup, but limits to ~1 concurrent connection |
| **Synchronous AI (no Celery)** | User waits 10-15 seconds, but simpler code |
| **Reuse existing CSS** | Minimal changes, but tight coupling to old portfolio |
| **Django admin for controls** | Less polished UI, but 100% feature-complete out of box |
| **No user auth (single admin)** | Can't handle multiple users, but fine for personal portfolio |

---

## Important Notes

1. **GitHub Rate Limiting**: 60 requests/hour unauthenticated, 5000/hour with token. We use token, so sync is safe to run multiple times daily.

2. **Gemini Costs**: ~$0.010 per project analysis (25 projects = $0.25 total). Monitor usage in Google Cloud Console.

3. **Data Migration**: Existing portfolio HTML stays as-is until new system is tested and deployed. No data loss risk.

4. **Fallback Logic**: If Gemini API fails, projects still display with basic GitHub data (repo_name, repo_description). AI content can be retried later.

5. **Deployment Checklist**:
   - [ ] Environment variables set on production
   - [ ] Database migrations run
   - [ ] Static files collected
   - [ ] Test API endpoints from browser
   - [ ] Test admin panel access
   - [ ] Verify GitHub sync works in production

---

## Questions During Implementation

If you hit blockers:
1. **GitHub API issues**: Check `GITHUB_TOKEN` validity and rate limits
2. **Gemini failures**: Validate API key and JSON response format
3. **Database errors**: Run `python manage.py migrate` and check SQLite file permissions
4. **Deployment issues**: Check Render/Fly.io logs and environment variables

---

## Timeline Estimate

| Task | Time | Status |
|------|------|--------|
| Setup Django + deps | 30 min | ⏳ |
| Create Project model | 20 min | ⏳ |
| GitHub sync command | 45 min | ⏳ |
| Gemini service | 1 hour | ⏳ |
| Admin interface | 20 min | ⏳ |
| DRF API | 30 min | ⏳ |
| Templates + UI | 1 hour | ⏳ |
| Local testing | 1 hour | ⏳ |
| Deploy | 30 min | ⏳ |
| **Total** | **~5-6 hours** | **Start now** |

---

## Next Steps

1. Confirm you're ready to start Step 1 (Django setup)
2. Provide GitHub Personal Access Token (I can guide creation)
3. Provide Gemini API Key (same)
4. Start implementation

Let's ship this. 🚀
