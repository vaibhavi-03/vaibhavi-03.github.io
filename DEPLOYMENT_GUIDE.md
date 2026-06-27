# Portfolio Deployment Guide — Vaibhavi Prajapati

---

## OPTION A: Deploy as Static HTML (Easiest — Do This First!)

Your `index.html` file is a complete standalone portfolio. No server needed.

### Step 1: GitHub Pages (Free, 5 minutes)
1. Create a GitHub account if you don't have one
2. Create a new repo named: `vaibhavi-prajapati.github.io`
3. Upload `index.html` to the repo root
4. Go to Settings → Pages → Source → main branch → Save
5. Your site is live at: `https://vaibhavi-prajapati.github.io` 🎉

### Step 2: Custom Domain (Optional, ~₹800/year)
- Buy a domain from GoDaddy or Namecheap (e.g. `vaibhaviprajapati.dev`)
- In GitHub Pages settings → Custom domain → enter your domain
- In your domain registrar → add a CNAME record pointing to your GitHub Pages URL

---

## OPTION B: Django + React (Full Stack — For Learning & Resume)

This is the version your friend built. Here's how to structure it:

### Project Structure
```
portfolio_project/
├── backend/          ← Django project
│   ├── portfolio/
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   ├── api/          ← Django app (REST API)
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── urls.py
│   │   └── serializers.py
│   ├── manage.py
│   └── requirements.txt
├── frontend/         ← React project
│   ├── src/
│   │   ├── components/
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── public/
│   └── package.json
└── README.md
```

### Setup Commands
```bash
# Backend (Django)
pip install django djangorestframework django-cors-headers
django-admin startproject portfolio .
python manage.py startapp api

# Frontend (React + Vite)
npm create vite@latest frontend -- --template react
cd frontend && npm install
npm install axios react-router-dom
```

### Django Settings (settings.py) — Key Parts
```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'corsheaders',
    'api',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',   # Must be first
    ...
]

CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",   # Vite dev server
    "https://your-frontend-domain.com",
]

STATIC_ROOT = BASE_DIR / 'staticfiles'
MEDIA_ROOT = BASE_DIR / 'media'
MEDIA_URL = '/media/'
```

### Django Models (api/models.py)
```python
from django.db import models

class Project(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    tech_stack = models.CharField(max_length=300)
    github_url = models.URLField(blank=True)
    live_url = models.URLField(blank=True)
    order = models.IntegerField(default=0)

    def __str__(self):
        return self.title

class Certification(models.Model):
    title = models.CharField(max_length=200)
    issuer = models.CharField(max_length=100)
    date_earned = models.DateField()
    credential_url = models.URLField(blank=True)

class BlogPost(models.Model):
    title = models.CharField(max_length=200)
    excerpt = models.TextField()
    url = models.URLField()   # Link to Medium/Hashnode
    tag = models.CharField(max_length=50)
    published_date = models.DateField()
```

### Django API Views (api/views.py)
```python
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Project, Certification, BlogPost
from .serializers import ProjectSerializer, CertSerializer, BlogSerializer

@api_view(['GET'])
def get_projects(request):
    projects = Project.objects.all().order_by('order')
    return Response(ProjectSerializer(projects, many=True).data)

@api_view(['GET'])
def get_certifications(request):
    certs = Certification.objects.all()
    return Response(CertSerializer(certs, many=True).data)

@api_view(['GET'])
def get_blogs(request):
    blogs = BlogPost.objects.all().order_by('-published_date')
    return Response(BlogSerializer(blogs, many=True).data)
```

### Fetch Data in React (src/components/Projects.jsx)
```jsx
import { useState, useEffect } from 'react'
import axios from 'axios'

const API_BASE = import.meta.env.VITE_API_URL || 'http://localhost:8000'

export default function Projects() {
  const [projects, setProjects] = useState([])

  useEffect(() => {
    axios.get(`${API_BASE}/api/projects/`)
      .then(res => setProjects(res.data))
      .catch(err => console.error(err))
  }, [])

  return (
    <section id="projects">
      {projects.map(p => (
        <div key={p.id} className="project-card">
          <h3>{p.title}</h3>
          <p>{p.description}</p>
        </div>
      ))}
    </section>
  )
}
```

---

## OPTION C: Deploy Django + React (Production)

### Recommended: Render.com (Free Tier)

#### Deploy Django Backend
1. Push backend to GitHub
2. Go to render.com → New → Web Service → Connect repo
3. Build Command: `pip install -r requirements.txt`
4. Start Command: `gunicorn portfolio.wsgi:application`
5. Add Environment Variables:
   - `SECRET_KEY` = your Django secret key
   - `DEBUG` = False
   - `DATABASE_URL` = (Render gives you a PostgreSQL URL)
6. Your API will be at: `https://your-backend.onrender.com`

#### Deploy React Frontend
1. Push frontend to GitHub
2. render.com → New → Static Site → Connect repo
3. Build Command: `npm run build`
4. Publish Directory: `dist`
5. Add Environment Variables:
   - `VITE_API_URL` = `https://your-backend.onrender.com`
6. Your site will be at: `https://your-frontend.onrender.com`

### Alternative: Vercel (Frontend) + Railway (Backend)
- **Vercel** is excellent for React — just connect GitHub, zero config
- **Railway** is great for Django — supports PostgreSQL, auto-deploys

---

## QUICK CHECKLIST Before Going Live

- [ ] Replace all `[ placeholder ]` text with real content
- [ ] Add your profile photo (`your-photo.jpg`)
- [ ] Add your resume PDF (`resume.pdf`) and update the link
- [ ] Replace social links (LinkedIn, GitHub, email, Twitter)
- [ ] Add real projects with actual GitHub/live links
- [ ] Add your certifications with real dates
- [ ] Update the stats (projects count, certs count, etc.)
- [ ] Test on mobile
- [ ] Add your portfolio URL to your LinkedIn profile

---

## Tips for a Strong Portfolio

1. **Quality > Quantity** — 3 polished projects beat 10 half-finished ones
2. **Always include GitHub links** — recruiters check code quality
3. **Add a live demo** — even if hosted for free on Render/Vercel
4. **Update regularly** — a portfolio that's clearly maintained signals activity
5. **Write at least one blog post** — it sets you apart from most students

---
*Built with 💚 for Vaibhavi Prajapati | B.Tech CSE 3rd Year*
