# ⚡ Quick Reference Guide - Portfolio CMS

## 🚀 Start the Server

```bash
cd portfolio-cms
python manage.py runserver
```

**Then visit:**
- 🌐 Portfolio: `http://localhost:8000/`
- 🔧 Admin: `http://localhost:8000/admin/`
- 📡 API: `http://localhost:8000/api/`

---

## 👤 Admin Login

```
URL: http://localhost:8000/admin/
Username: admin
Password: admin123
```

---

## ✅ Setup Checklist (5 Minutes)

### **1. Add About Profile** (1 min)
```
Admin > Projects > About Profiles > Add about profile
├── Professional Summary: "AI Engineer specialising in..."
├── Resume File: Upload your PDF
├── GitHub URL: https://github.com/m0inak057
├── LinkedIn URL: https://www.linkedin.com/in/moinakm/
└── Twitter URL: https://x.com/Moinak_05
Click: SAVE
```

### **2. Add Work Experience** (1 min)
```
Admin > Projects > Work Experiences > Add work experience
├── Job Title: "AI Engineer Intern"
├── Company: "Sole-arium Technologies"
├── Start Date: 2026-03-01
├── Is Current: ✓ (checked)
├── Description: "Developing ML and Computer Vision models..."
└── Order: 0
Click: SAVE
(Add more as needed)
```

### **3. Add Education** (1 min)
```
Admin > Projects > Educations > Add education
├── Degree: "B.Tech Computer Science - AI & ML"
├── Institution: "Jain (Deemed-to-be) University"
├── Start Date: 2023-08-15
├── End Date: 2027-06-15
├── GPA: 8.25
└── Order: 0
Click: SAVE
```

### **4. Add Skills** (2 min)
```
Admin > Projects > Skills > Add skill
For each skill (add 10-15):
├── Name: "Python"
├── Category: "Programming Language"
├── Proficiency: 90
├── Is Featured: ✓ (for top 5)
└── Order: 0
Click: SAVE and repeat
```

### **5. View on Portfolio**
```
Visit: http://localhost:8000/
(Refresh to see changes)
You should see:
✅ About section with summary
✅ Experience timeline
✅ Education section
✅ Skills with proficiency bars
```

---

## 🎛️ Admin Sections

| Section | Purpose | Location |
|---------|---------|----------|
| **Projects** | Manage GitHub projects | `/admin/projects/project/` |
| **Certificates** | Upload certificate PDFs | `/admin/projects/certificate/` |
| **📧 Contact Messages** | View visitor messages | `/admin/projects/contactmessage/` |
| **💬 Comments** | Moderate project comments | `/admin/projects/comment/` |
| **💼 Work Experiences** | Add job history | `/admin/projects/workexperience/` |
| **🎓 Educations** | Add education history | `/admin/projects/education/` |
| **👤 About Profile** | Your professional info | `/admin/projects/aboutprofile/` |
| **⭐ Skills** | Add technical skills | `/admin/projects/skill/` |

---

## 🔗 API Endpoints

### **Public Endpoints**
```bash
# Read-only endpoints (anyone can access)
GET /api/projects/                    # All visible projects
GET /api/projects/major/             # MAJOR projects only
GET /api/projects/other/             # OTHER projects only
GET /api/certificates/               # All certificates
GET /api/comments/                   # Approved comments only
GET /api/experience/                 # Work experiences
GET /api/education/                  # Education entries
GET /api/about/                      # About profile
GET /api/skills/                     # All skills
GET /api/skills/?is_featured=true    # Featured skills only
```

### **Form Submission Endpoints**
```bash
# Anyone can submit (public)
POST /api/contact/           # Submit contact form
POST /api/comments/          # Submit a comment (needs approval)
```

---

## 📝 Common Tasks

### **Add a New Job**
```
1. Admin > Work Experiences > + Add work experience
2. Fill: Job title, company, dates, description
3. Check "Is Current" if ongoing
4. Set order (0 appears first)
5. SAVE
```

### **Add a Skill**
```
1. Admin > Skills > + Add skill
2. Name: "Django"
3. Category: "Framework / Library"
4. Proficiency: 85 (as percentage)
5. Check "Is Featured" for top skills
6. SAVE
```

### **Moderate a Comment**
```
1. Admin > Comments
2. Click on comment to view
3. Change Status: PENDING → APPROVED
4. SAVE
(Approved comments show on portfolio)
```

### **Respond to Contact Message**
```
1. Admin > Contact Messages
2. Click on message to view full content
3. Change Status: NEW → READ or REPLIED
4. SAVE
(Then reply directly to their email)
```

### **Download Your Resume**
```
On Portfolio:
- Click "Download Resume/CV" button in About section
(You can change the file in Admin > About Profile)
```

---

## 🌐 Portfolio Sections

After setup, portfolio shows:

```
Hero Section
    ↓
About Me (from AboutProfile)
    ↓
Experience (from WorkExperience)
    ↓
Education (from Education)
    ↓
Skills (from Skill) ← Auto-grouped by category
    ↓
Projects (existing)
    ↓
Recent Comments (on projects)
    ↓
Certificates (existing)
    ↓
Contact Form (new)
    ↓
Social Links
    ↓
Footer
```

---

## 🔍 Filtering & Searching

### **In Admin**

**Contact Messages:**
- Filter by Status (NEW, READ, REPLIED, ARCHIVED)
- Search by name, email, subject, message

**Comments:**
- Filter by project and status (PENDING, APPROVED, REJECTED)
- Search by author name, email, content

**Skills:**
- Filter by category and Featured status
- Search by skill name

---

## 📱 Mobile Optimization

Your portfolio is fully mobile-responsive:
- ✅ Touch-friendly buttons
- ✅ Responsive grid layouts
- ✅ Mobile-friendly forms
- ✅ Clean typography
- ✅ Optimized images

---

## 🔐 Security Notes

✅ **CSRF Protection**: All forms protected  
✅ **Email Validation**: Forms validate emails  
✅ **Comment Moderation**: Prevents spam  
✅ **Status Control**: Hide unpublished content  

---

## 🐛 Troubleshooting

### **Server won't start?**
```bash
# Install missing packages
pip install -r requirements.txt

# Run migrations
python manage.py migrate

# Try again
python manage.py runserver
```

### **Admin won't load?**
```bash
# Create superuser if missing
python manage.py createsuperuser

# Then login at /admin/
```

### **Forms not submitting?**
- Check browser console for errors
- Verify CSRF token is in form
- Check server logs

### **Migrations failed?**
```bash
# Check migration status
python manage.py showmigrations

# Roll back if needed
python manage.py migrate projects 0002
```

---

## 📊 Database Backup

```bash
# Backup database
cp db.sqlite3 db.sqlite3.backup

# Restore if needed
cp db.sqlite3.backup db.sqlite3
```

---

## 🚀 Going Live

When deploying to production:

1. **Update .env file**:
   ```
   DEBUG=False
   ALLOWED_HOSTS=yourdomain.com,www.yourdomain.com
   SECRET_KEY=generate-new-secret-key
   ```

2. **Create `staticfiles/` folder**:
   ```bash
   python manage.py collectstatic --noinput
   ```

3. **Create superuser**:
   ```bash
   python manage.py createsuperuser
   ```

4. **Deploy** (Render, Fly.io, Heroku, etc.)

---

## 📞 Support

All features fully functional with:
- ✅ Django Admin Interface
- ✅ REST API Endpoints
- ✅ Django Templates
- ✅ Database Models
- ✅ Email Configuration (optional)

For advanced customization, see:
- `projects/models.py` - Data models
- `projects/views.py` - Business logic
- `projects/admin.py` - Admin interface
- `templates/portfolio/index.html` - Frontend

---

## ✨ You're All Set!

Your portfolio is now:
- ✅ Feature-complete
- ✅ Production-ready
- ✅ Mobile-optimized
- ✅ SEO-optimized
- ✅ Fully documented

**Now go add your content and start interviewing!** 🚀

Good luck! 🎉
