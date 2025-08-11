# Book Sharing Application

A Flask-based web application for sharing books within a community using an invite-code system.

## Core Features

- **User Management**: Registration with invite codes, user profiles with bios and aliases
- **Book Management**: Create, edit, hide/show books with ratings, categories (fiction/non-fiction), and metadata
- **Social Features**: Comments, upvotes, and book recommendations
- **Borrowing System**: Request-approval workflow for borrowing books with history tracking
- **Dashboard**: Public book discovery with filtering by availability and category

## Key Concepts

- **Invite System**: Users register with invite codes and receive their own code to share
- **Book Ownership**: Users own books and control borrowing permissions
- **Availability States**: Books can be available, borrowed, or hidden from public view
- **Community Focus**: Designed for trusted communities rather than anonymous public use

## User Workflows

1. **Registration**: Use invite code → Create account → Receive personal invite code
2. **Book Sharing**: Add book → Set visibility → Manage borrow requests
3. **Book Discovery**: Browse dashboard → Filter by category/availability → Request to borrow
4. **Borrowing**: Request book → Owner approves → Borrow → Return when done

# Project Structure

## Directory Organization

```
├── src/                    # Main application code
│   ├── app.py             # Application factory and configuration
│   ├── extensions.py      # Flask extensions initialization
│   ├── models/            # SQLAlchemy database models
│   │   ├── __init__.py
│   │   ├── user.py        # User model with authentication
│   │   ├── book.py        # Book model with relationships
│   │   ├── book_comment.py
│   │   ├── book_upvote.py
│   │   ├── borrow_request.py
│   │   ├── borrowing_history.py
│   │   └── invite_code.py
│   ├── routes/            # Flask blueprints for route handling
│   │   ├── __init__.py
│   │   ├── main.py        # Dashboard and public routes
│   │   ├── auth.py        # Authentication routes
│   │   ├── books.py       # Book management routes
│   │   └── profile.py     # User profile routes
│   ├── forms/             # WTForms form definitions
│   │   ├── __init__.py
│   │   ├── auth.py        # Login/registration forms
│   │   ├── book.py        # Book-related forms
│   │   └── profile.py     # Profile management forms
│   ├── templates/         # Jinja2 HTML templates
│   │   ├── base.html      # Base template with Bootstrap
│   │   ├── index.html     # Dashboard template
│   │   ├── auth/          # Authentication templates
│   │   ├── books/         # Book management templates
│   │   └── profile/       # Profile templates
│   └── static/            # Static assets
│       ├── css/
│       └── images/
├── data-model/            # Database schema documentation
│   └── book-share.yaml    # Complete data model specification
├── instance/              # Instance-specific files (created at runtime)
│   └── bookshare.db      # SQLite database file
├── run.py                 # Development server entry point
├── wsgi.py               # Production WSGI entry point
├── gunicorn_config.py    # Gunicorn configuration
├── requirements.txt      # Python dependencies
├── .env                  # Environment variables
└── Procfile              # Heroku deployment configuration
```

## Architecture Patterns

### Application Factory Pattern
- `src/app.py` contains `create_app()` function
- Extensions initialized in `src/extensions.py`
- Blueprints registered in application factory

### Blueprint Organization
- **main**: Dashboard and public routes (`/`)
- **auth**: Authentication routes (`/auth/`)
- **books**: Book management routes (`/books/`)
- **profile**: User profile routes (`/profile/`)

### Model Relationships
- **User**: Central model with relationships to books, comments, upvotes
- **Book**: Core entity with owner, borrower, comments, upvotes
- **Foreign Keys**: Proper CASCADE and SET NULL constraints
- **Lazy Loading**: Dynamic relationships for efficient queries

### Form Validation
- WTForms with custom validators
- CSRF protection enabled globally
- Server-side validation with user feedback

### Template Inheritance
- `base.html` provides common layout with Bootstrap
- Section-specific templates extend base
- Consistent navigation and styling

## Naming Conventions

- **Files**: snake_case (e.g., `book_comment.py`)
- **Classes**: PascalCase (e.g., `BookComment`)
- **Functions/Variables**: snake_case (e.g., `create_app`)
- **Database Tables**: snake_case (e.g., `book_comments`)
- **Routes**: kebab-case URLs (e.g., `/books/create`)
- **Templates**: snake_case (e.g., `book_detail.html`)

# Technology Stack

## Framework & Core Libraries

- **Flask 3.0.2**: Web framework with application factory pattern
- **SQLAlchemy 2.0.28**: ORM for database operations
- **Flask-SQLAlchemy 3.1.1**: Flask integration for SQLAlchemy
- **Flask-Migrate 4.0.5**: Database migration management
- **Flask-Login 0.6.3**: User session management and authentication
- **Flask-WTF 1.2.1**: Form handling with CSRF protection
- **WTForms 3.1.2**: Form validation and rendering

## Database & Deployment

- **SQLite**: Default database (configurable for other SQL databases)
- **Gunicorn 23.0.0**: WSGI HTTP server for production
- **python-dotenv 1.0.1**: Environment variable management

## Frontend

- **Bootstrap 5**: CSS framework for responsive UI
- **Jinja2 3.1.5**: Template engine (included with Flask)

## Development Commands

### Setup
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # macOS/Linux

# Install dependencies
pip install -r requirements.txt

# Initialize database
flask db init
flask db migrate -m "Initial migration"
flask db upgrade
```

### Development
```bash
# Run development server
python run.py
# or
flask run

# Database migrations
flask db migrate -m "Description of changes"
flask db upgrade
flask db downgrade  # if needed
```

### Production
```bash
# Run with Gunicorn
gunicorn -c gunicorn_config.py wsgi:application
# or simple
gunicorn wsgi:application
```

## Configuration

- Environment variables via `.env` file
- Flask configuration in `src/app.py`
- Gunicorn configuration in `gunicorn_config.py`
- Database path: `instance/bookshare.db`