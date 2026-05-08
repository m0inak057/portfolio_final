# Phase 2 Frontend Integration - Quick Start Test

**Status**: ✅ READY TO TEST

---

## Quick Test (5 Minutes)

### 1. Start Django Server
```bash
cd portfolio-cms
source venv/Scripts/activate
# Windows: venv\Scripts\activate.bat

python manage.py runserver
```

You should see:
```
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.
```

### 2. Visit Your Portfolio Homepage
- Open browser: **http://localhost:8000/**
- You should see the complete portfolio page with:
  - Navigation sidebar
  - Hero section
  - About me section
  - Skills section
  - Experience section
  - "MY WORK" section (currently empty because no projects are visible yet)
  - Certifications
  - Education
  - Contact form
  - Social links

### 3. Make Projects Visible
- Open admin: **http://localhost:8000/admin/**
- Login with credentials (admin / admin123)
- Click "Projects"
- You should see all synced GitHub repos
- **Click the red X icon** next to 2-3 projects to make them visible
- **Set category**: Change some to "MAJOR" for featured display
- Click "Save"

### 4. Refresh Portfolio
- Go back to: **http://localhost:8000/**
- **MY WORK** section should now show your visible projects!
- Click "Other Projects" button to see the modal with more projects

### 5. Verify API Endpoints
```bash
curl http://localhost:8000/api/projects/
curl http://localhost:8000/api/projects/major/
curl http://localhost:8000/api/projects/other/
```

---

## Expected Output

**Portfolio Page Shows**:
```
MAJOR PROJECTS (in grid):
├── Project Title (AI Generated)
├── Project Summary
├── Key Features (from AI)
└── GitHub Link

OTHER PROJECTS (in modal):
├── Project Title
├── Project Summary  
├── Tech Stack
└── GitHub Link (from your repos)
```

---

## Troubleshooting

### If you see "No major projects to display yet..."
**Solution**: Go to admin and mark some projects as `is_visible=true` and set their `category=MAJOR`

### If CSS/Images don't load
**Solution**: Run `python manage.py collectstatic --noinput`

### If template not found error
**Solution**: Verify file exists: `portfolio-cms/templates/portfolio/index.html`

### If 404 error on homepage
**Solution**: Verify URL is: `http://localhost:8000/` (with trailing slash)

---

## File Locations if You Need to Modify

- **Frontend Template**: `portfolio-cms/templates/portfolio/index.html`
- **Styling**: `portfolio-cms/static/css/styles.css`
- **JavaScript**: `portfolio-cms/static/js/script.js`
- **Portfolio View**: `portfolio-cms/projects/views.py`
- **URL Config**: `portfolio-cms/portfolio_cms/urls.py`

---

## Next: What Works Now

✅ Portfolio displays with dynamic project data  
✅ MAJOR/OTHER categorization working  
✅ Admin control over visibility  
✅ API endpoints serving JSON data  
✅ Static files (CSS, JS, images) properly served  
✅ Responsive design preserved  

---

## Ready? Let's Test! 🚀

```bash
cd portfolio-cms
source venv/Scripts/activate
python manage.py runserver
# Visit http://localhost:8000/
```

Enjoy your new dynamic portfolio! 🎉
