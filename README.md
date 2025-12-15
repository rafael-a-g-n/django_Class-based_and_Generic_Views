# Django Class-Based and Generic Views

## 📚 Overview

This is an educational Django project that demonstrates the migration from **function-based views** to **class-based views** and **generic views**. The project implements a simple online course management system showcasing how Django's class-based views (`ListView`, `DetailView`, and `View`) provide a more structured and reusable approach to handling HTTP requests.

## 🎯 Learning Objectives

This project demonstrates:
- Converting function-based views to class-based views
- Using Django's generic views (`ListView`, `DetailView`)
- Implementing custom class-based views using the base `View` class
- Handling GET and POST requests with class-based views
- Working with Django ORM relationships (ForeignKey, ManyToMany)
- Managing static files and media uploads
- Template rendering with context data

## ✨ Features

- **Course Listing**: Browse the top 10 most popular courses (ordered by enrollment count)
- **Course Details**: View detailed information about a course including its lessons
- **Course Enrollment**: Enroll in courses with automatic enrollment counter
- **Admin Interface**: Full Django admin panel for managing courses, instructors, learners, and lessons
- **Image Upload**: Course images with Pillow support
- **Responsive Templates**: HTML templates with CSS styling

## 🏗️ Project Structure

```
Class-based_and_Generic_Views/
├── manage.py                           # Django management script
├── db.sqlite3                          # SQLite database
├── requirements.txt                    # Python dependencies
├── runtime.txt                         # Python version specification
│
├── myproject/                          # Main project configuration
│   ├── __init__.py
│   ├── settings.py                     # Django settings
│   ├── urls.py                         # Root URL configuration
│   ├── wsgi.py                         # WSGI configuration
│   └── asgi.py                         # ASGI configuration
│
├── onlinecourse/                       # Main application
│   ├── models.py                       # Data models (Course, Lesson, Instructor, etc.)
│   ├── views.py                        # Class-based views
│   ├── urls.py                         # App URL patterns
│   ├── admin.py                        # Admin configuration
│   ├── apps.py                         # App configuration
│   ├── tests.py                        # Test cases
│   │
│   ├── templates/
│   │   └── onlinecourse/
│   │       ├── course_list.html        # Course listing template
│   │       └── course_detail.html      # Course details template
│   │
│   ├── static/
│   │   └── onlinecourse/
│   │       └── course.css              # Custom CSS styling
│   │
│   └── migrations/                     # Database migrations
│       └── 0001_initial.py
│
└── static/                             # Collected static files
    ├── admin/                          # Django admin static files
    └── media/
        └── course_images/              # Uploaded course images
```

## 🛠️ Tech Stack

- **Python**: 3.8.13 (specified in `runtime.txt`)
- **Django**: 4.2.17 (web framework)
- **SQLite**: Default database (included with Python)
- **Pillow**: 10.4.0 (image processing)
- **Gunicorn**: 22.0.0 (WSGI HTTP server for production)
- **Jinja2**: 3.1.4 (templating engine support)

## 📋 Data Models

### Course
- Name, description, publication date
- Image upload field
- Total enrollment counter
- Many-to-many relationships with instructors and users

### Lesson
- Title, order, content
- Foreign key to Course

### Instructor
- Foreign key to Django User
- Full-time status
- Total learners count

### Learner
- Foreign key to Django User
- Occupation (Student, Developer, Data Scientist, Database Admin)
- Social link

### Enrollment
- Links users to courses
- Enrollment mode (Audit, Honor, Beta)
- Date enrolled
- Rating

## 🔍 Class-Based Views Implementation

### 1. CourseListView (ListView)
```python
class CourseListView(generic.ListView):
    template_name = 'onlinecourse/course_list.html'
    context_object_name = 'course_list'
    
    def get_queryset(self):
        return Course.objects.order_by('-total_enrollment')[:10]
```
**Replaces**: Function-based `popular_course_list` view  
**URL**: `/onlinecourse/`  
**Purpose**: Display top 10 courses by enrollment

### 2. CourseDetailsView (DetailView)
```python
class CourseDetailsView(generic.DetailView):
    model = Course
    template_name = 'onlinecourse/course_detail.html'
```
**Replaces**: Function-based `course_details` view  
**URL**: `/onlinecourse/course/<int:pk>/`  
**Purpose**: Show course details and related lessons

### 3. EnrollView (View)
```python
class EnrollView(View):
    def post(self, request, *args, **kwargs):
        course_id = kwargs.get('pk')
        course = get_object_or_404(Course, pk=course_id)
        course.total_enrollment += 1
        course.save()
        return HttpResponseRedirect(reverse('onlinecourse:course_details', args=(course.id,)))
```
**Replaces**: Function-based `enroll` view  
**URL**: `/onlinecourse/course/<int:pk>/enroll/`  
**Purpose**: Handle course enrollment (POST only)

## 🚀 Getting Started

### Prerequisites
- Python 3.8 or higher
- pip (Python package installer)
- Virtual environment (recommended)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd Class-based_and_Generic_Views
   ```

2. **Create and activate a virtual environment**
   
   **Windows (PowerShell/CMD):**
   ```bash
   python -m venv .venv
   .venv\Scripts\activate
   ```
   
   **Windows (Git Bash):**
   ```bash
   python -m venv .venv
   source .venv/Scripts/activate
   ```
   
   **macOS/Linux:**
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

4. **Apply database migrations**
   ```bash
   python manage.py migrate
   ```

5. **Create a superuser (for admin access)**
   ```bash
   python manage.py createsuperuser
   ```
   Follow the prompts to set username, email, and password.

6. **Collect static files**
   ```bash
   python manage.py collectstatic --noinput
   ```

7. **Run the development server**
   ```bash
   python manage.py runserver
   ```

8. **Access the application**
   - Course List: http://127.0.0.1:8000/onlinecourse/
   - Admin Panel: http://127.0.0.1:8000/admin/
   - Use the superuser credentials to log in to the admin panel

## 📖 Usage

### Viewing Courses
1. Navigate to `http://127.0.0.1:8000/onlinecourse/`
2. Browse the list of available courses
3. Each course displays:
   - Course image
   - Course name
   - Description
   - Total enrollment count
   - Enroll button

### Enrolling in a Course
1. Click the "Enroll" button on any course
2. The enrollment count will increment
3. You'll be redirected to the course details page

### Viewing Course Details
1. Click on a course or navigate to `/onlinecourse/course/<id>/`
2. View course information and all associated lessons
3. Lessons are displayed in order with their content

### Managing Content (Admin)
1. Log in to the admin panel at `http://127.0.0.1:8000/admin/`
2. Add/edit/delete:
   - Courses (with image uploads)
   - Lessons
   - Instructors
   - Learners
   - Enrollments

## 🧪 Testing

Run the test suite:
```bash
python manage.py test onlinecourse
```

Check for any issues:
```bash
python manage.py check
```

## 📁 Static Files & Media

### Static Files
- **CSS**: `onlinecourse/static/onlinecourse/course.css`
- **Collected**: All static files are collected to `static/` directory
- **Admin**: Django admin static files included

### Media Files
- **Upload Path**: `static/media/course_images/`
- **Setting**: `MEDIA_URL = '/media/'`
- **Purpose**: Store course images uploaded through admin

### Configuration
```python
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
MEDIA_ROOT = os.path.join(STATIC_ROOT, 'media')
MEDIA_URL = '/media/'
```

## 🔧 Configuration

### Key Settings (myproject/settings.py)

- **Debug Mode**: `DEBUG = True` (set to `False` in production)
- **Allowed Hosts**: `ALLOWED_HOSTS = []` (configure for production)
- **Database**: SQLite (for development)
- **Installed Apps**: Includes `onlinecourse` and Django contrib apps
- **Secret Key**: Change this in production!

### URL Configuration

**Root URLs** (`myproject/urls.py`):
- `/admin/` → Django admin interface
- `/onlinecourse/` → Online course application

**App URLs** (`onlinecourse/urls.py`):
- `/onlinecourse/` → Course list view
- `/onlinecourse/course/<int:pk>/` → Course detail view
- `/onlinecourse/course/<int:pk>/enroll/` → Enrollment endpoint

## 🚢 Deployment

### Preparation
1. Set `DEBUG = False` in settings.py
2. Configure `ALLOWED_HOSTS` with your domain
3. Change `SECRET_KEY` to a secure random string
4. Set up environment variables for sensitive data
5. Configure a production database (PostgreSQL recommended)
6. Set up proper static file serving (WhiteNoise, CDN, or web server)

### Using Gunicorn (Production WSGI Server)
```bash
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
```

### Considerations
- Use a reverse proxy (Nginx, Apache)
- Set up HTTPS/SSL certificates
- Configure database backups
- Set up media file storage (AWS S3, Azure Blob, etc.)
- Use environment variables for configuration
- Set up logging and monitoring

## 🐛 Troubleshooting

### Issue: Images not displaying
**Solution**: 
- Ensure Pillow is installed: `pip install Pillow`
- Check that `media/course_images/` directory exists
- Verify `MEDIA_URL` and `MEDIA_ROOT` settings
- Run `python manage.py collectstatic`

### Issue: Static files not loading
**Solution**:
- Run `python manage.py collectstatic`
- Check `STATIC_URL` and `STATIC_ROOT` settings
- Verify CSS files exist in `onlinecourse/static/onlinecourse/`

### Issue: Admin panel styles missing
**Solution**:
- Run `python manage.py collectstatic --clear`
- Ensure Django is properly installed

### Issue: 404 errors on course pages
**Solution**:
- Check URL patterns in `onlinecourse/urls.py`
- Verify courses exist in database (add via admin)
- Check URL name references match: `onlinecourse:course_details`

### Issue: ModuleNotFoundError
**Solution**:
- Activate virtual environment
- Install requirements: `pip install -r requirements.txt`

## 🤝 Contributing

This is an educational project. Feel free to:
- Fork the repository
- Create feature branches
- Submit pull requests
- Report issues
- Suggest improvements

## 📚 Learning Resources

- [Django Class-Based Views Documentation](https://docs.djangoproject.com/en/stable/topics/class-based-views/)
- [Django Generic Views](https://docs.djangoproject.com/en/stable/ref/class-based-views/generic-display/)
- [Django Models Documentation](https://docs.djangoproject.com/en/stable/topics/db/models/)
- [Django Templates](https://docs.djangoproject.com/en/stable/topics/templates/)

## 📝 License

This project is for educational purposes as part of the IBM Django course on Coursera.

## 👨‍💻 Author

Created as part of the IBM Django course curriculum focusing on class-based and generic views.

---

**Happy Learning! 🎓**
