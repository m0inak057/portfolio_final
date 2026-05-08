# Certificate System - Quick Verification Guide

**Goal**: Verify the Certificate Management System is working correctly  
**Time**: 10 minutes  
**Date**: March 31, 2026

---

## ✅ Step 1: Check Database Created

### Command:
```bash
cd portfolio-cms
python manage.py shell
```

### In Python Shell:
```python
from projects.models import Certificate
print("Certificate table exists:", Certificate.objects.model._meta.db_table)
exit()
```

### Expected Output:
```
Certificate table exists: projects_certificate
```

---

## ✅ Step 2: Start Django Server

### Command:
```bash
cd portfolio-cms
python manage.py runserver
```

### Expected Output:
```
Django version 4.2.x
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

---

## ✅ Step 3: Access Admin Panel

### URL:
```
http://localhost:8000/admin/
```

### What You Should See:
- ✅ Django admin login page
- ✅ Username field
- ✅ Password field

### Login:
- Username: `admin`
- Password: `admin123`

---

## ✅ Step 4: Check Certificates Section

### After Login:
1. Look at the left sidebar
2. Find **"Projects"** app section
3. You should see:
   ```
   PROJECTS
   ├── Certificates    ← NEW!
   └── Projects
   ```

### Click "Certificates":
```
http://localhost:8000/admin/projects/certificate/
```

### What You Should See:
- ✅ Empty certificate list (no certificates yet)
- ✅ Green "+ Add certificate" button (top right)
- ✅ Search box
- ✅ "Filters" button
- ✅ Column headers: Name, Issuer, Issued Date, Visibility, File, Created At

---

## ✅ Step 5: Add Your First Certificate

### Click "+ Add certificate"

### Fill in the form:
```
Name:           AWS Solutions Architect
Issuer:         Amazon Web Services
Issued Date:    2024-12-15  (click calendar icon)
PDF File:       [Choose File] → select any PDF
Is Visible:     [✓] Check the checkbox
```

### Click "SAVE"

### Expected Result:
```
✅ Success! The Certificate "AWS Solutions Architect" was added successfully.
```

---

## ✅ Step 6: Verify Certificate in Admin List

### You should see:
```
Certificate List (1 certificate)

NAME                          ISSUER                   DATE         VISIBILITY  FILE
AWS Solutions Architect       Amazon Web Services      Dec 15, 2024  ✓ Visible  📄 PDF
```

### Click on the certificate name to verify you can edit it

---

## ✅ Step 7: Check Portfolio Website

### URL:
```
http://localhost:8000/
```

### Scroll down to "CERTIFICATIONS" section

### What You Should See:
```
CERTIFICATIONS
┌──────────────────────────────┐
│ 🏆 AWS Solutions Architect   │
│     Amazon Web Services      │
│     Dec 2024                 │
│     View Certificate → (PDF) │
└──────────────────────────────┘
```

### Try to click "View Certificate" link
- Should open PDF in new tab (or download)

---

## ✅ Step 8: Check API Endpoint

### URL:
```
http://localhost:8000/api/certificates/
```

### What You Should See:
```json
{
  "count": 1,
  "next": null,
  "previous": null,
  "results": [
    {
      "id": 1,
      "name": "AWS Solutions Architect",
      "issuer": "Amazon Web Services",
      "issued_date": "2024-12-15",
      "pdf_file": "/media/certificates/pdfs/your_pdf_file.pdf",
      "is_visible": true,
      "created_at": "2024-03-31T10:30:00Z",
      "updated_at": "2024-03-31T10:30:00Z"
    }
  ]
}
```

---

## ✅ Step 9: Test Toggle Visibility

### Go back to admin:
```
http://localhost:8000/admin/projects/certificate/
```

### Click the visibility badge:
- Green ✓ → Click → Turns Red ✕
- Red ✕ → Click → Turns Green ✓

### Go to portfolio:
```
http://localhost:8000/
```

### After toggling to hidden (red ✕):
- Certificate should disappear from portfolio

### After toggling to visible (green ✓):
- Certificate should reappear on portfolio

---

## ✅ Step 10: Test Search

### In admin certificates list:

### Type in search box:
```
"AWS"
```

### Expected:
- Certificate appears in results

### Type something not in name/issuer:
```
"Google"
```

### Expected:
- No results shown

---

## ✅ Step 11: Test Filter

### Click "Filters" button (right side)

### Click "Is Visible"
- Select "True"
- List shows only visible certificates

### Click "Is Visible"
- Select "False"
- List shows only hidden certificates

---

## 📋 Complete Checklist

Copy this and check off as you complete each step:

```
✓ Step 1: Database created
✓ Step 2: Django server running
✓ Step 3: Admin panel accessible
✓ Step 4: Certificates section visible in admin
✓ Step 5: Can add new certificate
✓ Step 6: Certificate appears in admin list
✓ Step 7: Certificate displays on portfolio
✓ Step 8: API endpoint returns certificate data
✓ Step 9: Visibility toggle works
✓ Step 10: Search works
✓ Step 11: Filter works
```

---

## 🎯 If Everything Worked

**Congratulations!** Your Certificate Management System is fully operational:

✅ Database schema created  
✅ Admin interface working  
✅ Can upload PDFs  
✅ Can toggle visibility  
✅ Portfolio displays certificates  
✅ API serves certificates  
✅ Search/filter functional  

**You're ready to add your real certificates!**

---

## ⚠️ If Something Didn't Work

### Problem: Admin section shows error
```bash
# Run this
python manage.py migrate
# Then refresh admin page
```

### Problem: Media files not serving
```
Check MEDIA_URL and MEDIA_ROOT in settings.py
Restart Django server
```

### Problem: Certificate not showing on portfolio
```
1. Go to admin
2. Click on certificate
3. Verify "Is Visible" checkbox is CHECKED
4. Click SAVE
5. Refresh portfolio page (Ctrl+Shift+R)
```

### Problem: PDF upload fails
```
1. Check file size (must be under 10MB)
2. Ensure file is valid PDF
3. Check media/ folder exists
4. Check permissions on media/ folder
```

---

## 📚 For More Details

- **Complete Guide**: See `portfolio-cms/CERTIFICATE_MANAGEMENT_GUIDE.md`
- **Technical Summary**: See `CERTIFICATE_SYSTEM_SUMMARY.md`
- **Implementation Details**: See docstrings in `projects/models.py`

---

## 🚀 Next Steps After Verification

1. **Add all your real certificates** via admin panel
2. **Customize which ones are visible** using the toggle
3. **Check API data** in your frontend integration
4. **Enjoy your dynamic portfolio!**

---

**Questions?** Check the troubleshooting section in `CERTIFICATE_MANAGEMENT_GUIDE.md`

