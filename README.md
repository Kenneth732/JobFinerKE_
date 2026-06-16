

# JobFinder KE

### Premium Access to Government-Registered Startup Opportunities in Kenya

[![Django Version](https://img.shields.io/badge/Django-6.0.3-092e20?logo=django)](https://www.djangoproject.com/)
[![Python Version](https://img.shields.io/badge/Python-3.12-3776ab?logo=python)](https://www.python.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791?logo=postgresql)](https://www.postgresql.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)



## 📖 Overview

**JobFinder KE** is a premium job platform that provides **48-hour early access** to job openings from government-registered startups and live government tenders in Kenya. Subscribers receive opportunities before they're released to the public, giving them a significant competitive advantage in the job market.

###  Key Features

- ** 48-Hour Early Access** – Get jobs before public release
- ** Government Verified** – All employers verified through Business Registration Service (BRS)
- ** Live Tender Alerts** – Real-time government procurement notifications
- ** Multi-Channel Notifications** – Email, SMS, and WhatsApp alerts
- ** M-Pesa Integration** – Seamless subscription payments
- ** Automated Web Scraping** – Continuous job sourcing from bebee.com
- ** Admin Dashboard** – Manual import controls and monitoring

---

##  Architecture

```
JobFinder KE
├── Django 6.0.3 Backend
├── PostgreSQL Database
├── Celery + Redis (Background Tasks)
├── Django REST Framework (API endpoints)
├── BeautifulSoup / Requests (Job scraping)
├── Allauth (Social authentication)
└── Custom CSS (jfke- scoped styling)
```

---

##  Prerequisites

- Python 3.12+
- PostgreSQL 16+
- Redis (for Celery)
- Virtual environment (recommended)

---

##  Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/jobfinder-ke.git
cd jobfinder-ke
```

### 2. Create Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables

Create a `.env` file in the project root:

```env
# Database
DB_NAME=jobs
DB_USER=your_username
DB_PASSWORD=your_password
DB_HOST=localhost
DB_PORT=5432

# Google OAuth
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret

# Email
EMAIL_HOST_USER=noreply@jobfinder.ke
EMAIL_HOST_PASSWORD=your_email_password

# M‑Pesa
MPESA_CONSUMER_KEY=your_consumer_key
MPESA_CONSUMER_SECRET=your_consumer_secret
MPESA_SHORTCODE=888888

# Security
DJANGO_SECRET_KEY=your_secret_key
DEBUG=True
```

### 5. Setup Database

```bash
sudo -u postgres psql
CREATE DATABASE jobs;
CREATE USER your_username WITH PASSWORD 'your_password';
ALTER ROLE your_username SET client_encoding TO 'utf8';
ALTER ROLE your_username SET default_transaction_isolation TO 'read committed';
ALTER ROLE your_username SET timezone TO 'Africa/Nairobi';
GRANT ALL PRIVILEGES ON DATABASE jobs TO your_username;
\q

python manage.py makemigrations
python manage.py migrate
```

### 6. Create Superuser

```bash
python manage.py createsuperuser
```

### 7. Collect Static Files

```bash
python manage.py collectstatic
```

### 8. Run Development Server

```bash
python manage.py runserver
```

Visit `http://127.0.0.1:8000`

---

## 🔧 Data Import & Automation

### Manual Import via Admin Dashboard

Access: `http://127.0.0.1:8000/admin/` then use the custom admin actions.

### Command Line Import

```bash
# Import jobs from bebee.com (30 pages = ~600 jobs)
python manage.py import_bebee_jobs --pages 30

# Dry run (preview without saving)
python manage.py import_bebee_jobs --pages 5 --dry-run
```

### Automated Cron Jobs

Add to crontab:

```bash
0 2 * * * cd /path/to/jobfinder-ke && python manage.py import_bebee_jobs --pages 30 >> /var/log/job_import.log 2>&1
```

---

##  Project Structure

```
jobfinder-ke/
├── job_orm/                 # Project configuration
│   ├── settings.py
│   ├── urls.py
│   └── celery.py
├── jobs/                    # Main application
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   ├── admin.py
│   ├── bebee_scraper.py
│   ├── management/
│   │   └── commands/
│   │       └── import_bebee_jobs.py
│   └── templates/jobs/
├── templates/               # Global templates
│   ├── base.html
│   ├── index.html
│   ├── pricing.html
│   └── subscribe.html
├── static/                  # Static files (CSS, JS, images)
├── media/                   # User uploaded files
└── requirements.txt
```

---

##  Database Schema

```python
User (Custom)
├── role (job_seeker/employer/admin)
├── phone_number (unique)
├── is_subscribed
└── subscription_end_date

EmployerProfile
├── company_name
├── business_registration_number (unique)
├── kra_pin
├── sector
├── is_verified (government verified)
└── jobs (ForeignKey)

JobPosting
├── title, description, requirements
├── location, salary_range
├── application_deadline
├── is_pre_public (48‑hour early access)
├── public_at
└── status (draft/published/closed)

GovernmentTender
├── tender_number (unique)
├── title, description
├── ministry, value_estimate
├── deadline, published_at
└── is_active

UserSubscription
├── user, amount_paid
├── mpesa_transaction_id (unique)
├── start_date, end_date
└── is_active

JobApplication
├── job, applicant
├── cover_letter, cv_file
├── status
└── is_priority
```

---

##  Data Sources

| Source | Type | Frequency | Method |
|--------|------|-----------|--------|
| bebee.com | Jobs | Every 6 hours | API (REST) |
| BRS Kenya | Startups | Daily | Web scraping (planned) |
| KEPSA | Member Jobs | Weekly | Web scraping (planned) |

---

##  Frontend Features

- Responsive design (mobile, tablet, desktop)
- Live job feed with slide‑in animations
- Animated stats counters (from live API)
- Early access badges
- M‑Pesa payment flow
- Scoped CSS (`jfke-` prefix) – no conflicts with other pages

---

##  Authentication & Security

- Google OAuth2 (social login)
- Phone number verification
- Role‑based access (job seeker / employer / admin)
- CSRF protection
- SQL injection prevention (Django ORM)
- XSS protection (auto‑escaping)

---

##  Subscription & Payments

**Pricing Plans:**
- Monthly: KES 500 / month
- Annual:  KES 5,000 / year (17% savings)

**Payment Methods:**
- M‑Pesa (PayBill number: 888888)
- Card payments (coming soon)

**Features:**
- 7‑day free trial
- Cancel anytime
- Automatic billing
- Transaction history

---

##  API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/stats/` | GET | Live stats (jobs, tenders, subscribers) |
| `/api/jobs/` | GET | Job listings with filters |
| `/api/tenders/` | GET | Tender listings |
| `/api/subscribe/` | POST | Subscription activation |

---

##  Testing

```bash
# Run all tests
python manage.py test

# Run jobs app tests
python manage.py test jobs

# With coverage
coverage run manage.py test
coverage report
coverage html
```

---

## 📈 Performance Optimizations

- Database indexes on foreign keys and frequently filtered fields
- `select_related()` and `prefetch_related()` for related objects
- Redis caching for session storage and API responses
- Pagination (20 items per page)
- Lazy loading of images
- CDN for fonts and static assets

---

## 🐛 Troubleshooting

**No jobs on /jobs/**

```bash
python manage.py shell -c "from jobs.models import JobPosting; from django.utils import timezone; print(JobPosting.objects.filter(status='published', application_deadline__gt=timezone.now()).count())"
python manage.py import_bebee_jobs --pages 5
```

**Cron jobs not running**

```bash
sudo systemctl status cron
tail -f /var/log/syslog | grep CRON
```

**Sector filter shows no results**

Run the sector updater script (provided in the codebase) to assign sectors from job titles.

---

## 🚀 Deployment (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install nginx postgresql redis-server supervisor

pip install gunicorn
gunicorn --bind 0.0.0.0:8000 job_orm.wsgi:application

sudo nano /etc/nginx/sites-available/jobfinder
```

Nginx config:

```nginx
server {
    listen 80;
    server_name jobfinder.ke;
    
    location /static/ {
        alias /path/to/static/;
    }
    
    location /media/ {
        alias /path/to/media/;
    }
    
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/jobfinder /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

---

## 📝 Environment Variables Reference

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `DB_NAME` | Yes | – | PostgreSQL database name |
| `DB_USER` | Yes | – | Database username |
| `DB_PASSWORD` | Yes | – | Database password |
| `GOOGLE_CLIENT_ID` | Yes | – | Google OAuth client ID |
| `GOOGLE_CLIENT_SECRET` | Yes | – | Google OAuth secret |
| `MPESA_CONSUMER_KEY` | For payments | – | M‑Pesa API key |
| `MPESA_CONSUMER_SECRET` | For payments | – | M‑Pesa API secret |
| `EMAIL_HOST_USER` | For digests | – | SMTP username |
| `EMAIL_HOST_PASSWORD` | For digests | – | SMTP password |
| `DJANGO_SECRET_KEY` | Yes | – | Django secret key |
| `DEBUG` | No | False | Debug mode |

---

##  Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

Coding standards:
- PEP 8
- Black formatting
- Docstrings for functions
- Tests for new features

---

##  License

Distributed under the MIT License. See `LICENSE` for more information.

---

##  Contact & Support

- **Email**: support@jobfinder.ke
- **Phone**: +254 742 164 615
- **WhatsApp**: +254 742 164 615
- **Website**: [https://jobfinder.ke](https://jobfinder.ke)

---

## 🙏 Acknowledgments

- Business Registration Service (BRS) Kenya
- Kenya Private Sector Alliance (KEPSA)
- bebee for job listings
- All our subscribers and partners

---

**Built with ❤️ for Kenyan job seekers and businesses**

*"First Access. Zero Competition."*
