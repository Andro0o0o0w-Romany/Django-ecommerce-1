# TrustBuy

A full-featured e-commerce marketplace built with Django where sellers can list products and buyers can browse, search, and purchase items via PayPal integration. Styled with Tailwind CSS for a modern, responsive UI.

---

## Table of Contents

- [Features](#features)
- [Architecture Overview](#architecture-overview)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Models](#models)
- [URL Endpoints](#url-endpoints)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Screenshots Workflow](#screenshots-workflow)
- [License](#license)

---

## Features

- **User Authentication** -- Full registration, login, and logout system built on Django's `auth` framework
- **User Profiles** -- Users can create profiles with a profile picture and phone number
- **Product Listings** -- Sellers can create, update, and delete product listings with images
- **Product Search** -- Real-time search functionality to filter products by name
- **Pagination** -- Browseable product catalog with paginated results (3 per page)
- **Product Details** -- Dedicated detail page for each product showing full specs, seller info, and contact details
- **My Listings** -- Dashboard for sellers to manage their own product listings (edit/delete)
- **Seller Profiles** -- Public-facing seller profile pages accessible from product details
- **PayPal Checkout** -- Integrated PayPal Smart Payment Buttons for seamless purchases
- **Admin Panel** -- Customized Django admin with product search and display configuration
- **Responsive Design** -- Tailwind CSS utility classes for mobile-first responsive layouts

---

## Architecture Overview

TrustBuy follows the standard **Django MVT (Model-View-Template)** architecture with two main apps:

```
                    +-----------------------+
                    |      TrustBuy         |
                    |   (Project Config)    |
                    |  settings, urls, wsgi |
                    +-----------+-----------+
                                |
                 +--------------+--------------+
                 |                             |
        +--------v--------+          +--------v--------+
        |   product app   |          |    User app     |
        +-----------------+          +-----------------+
        | Models:         |          | Models:         |
        |  - product      |          |  - Profile      |
        |  - Logo         |          |                 |
        | Views:          |          | Views:          |
        |  - List (FBV)   |          |  - register     |
        |  - Detail (CBV) |          |  - profile      |
        |  - Create (CBV) |          |  - create_profile|
        |  - Update (CBV) |          |  - sellerprofile|
        |  - Delete (CBV) |          |                 |
        |  - My Listings  |          | Forms:          |
        +-----------------+          |  - NewUserForm  |
                                     +-----------------+
```

The project uses both **Function-Based Views (FBV)** and **Class-Based Views (CBV)** -- demonstrating both approaches side by side. Class-based generic views (`ListView`, `DetailView`, `CreateView`, `UpdateView`, `DeleteView`) handle the core CRUD operations.

---

## Tech Stack

| Layer        | Technology                          |
|--------------|-------------------------------------|
| Backend      | Python 3.9, Django 4.0.5            |
| Database     | SQLite3 (default)                   |
| Frontend     | Django Templates, Tailwind CSS 2.2  |
| Payments     | PayPal Smart Payment Buttons (JS SDK) |
| Auth         | Django built-in `auth` framework    |
| Media        | Django `ImageField` + file uploads  |
| Admin        | Django Admin (customized)           |

---

## Project Structure

```
Django-ecommerce-1/
|
+-- TrustBuy/                   # Django project configuration
|   +-- __init__.py
|   +-- settings.py             # Project settings (DB, apps, middleware, media)
|   +-- urls.py                 # Root URL configuration
|   +-- wsgi.py                 # WSGI entry point
|   +-- asgi.py                 # ASGI entry point
|
+-- product/                    # Product app -- core marketplace functionality
|   +-- models.py               # Product and Logo models
|   +-- views.py                # FBV + CBV views for product CRUD
|   +-- urls.py                 # Product URL routes
|   +-- admin.py                # Custom admin config with search & display
|   +-- apps.py                 # App configuration
|   +-- templates/product/
|   |   +-- base.html           # Base template with navbar
|   |   +-- index.html          # Homepage with product grid & search
|   |   +-- Product_detail.html # Product detail + PayPal checkout
|   |   +-- AddProduct.html     # Add product form (FBV)
|   |   +-- product_form.html   # Add/Update product form (CBV)
|   |   +-- Edit_product.html   # Edit product form (FBV)
|   |   +-- DeleteProduct.html  # Delete confirmation (FBV)
|   |   +-- product_confirm_delete.html  # Delete confirmation (CBV)
|   |   +-- mylistings.html     # Seller's product management dashboard
|   |   +-- UpdateProduct.html  # Update product form (FBV)
|   +-- static/product/
|       +-- src/tw.css          # Tailwind CSS source
|       +-- styles.css          # Compiled Tailwind CSS output
|
+-- User/                       # User app -- authentication & profiles
|   +-- models.py               # Profile model (extends Django User)
|   +-- views.py                # Registration, profile, seller profile views
|   +-- urls.py                 # Auth URL routes (register, login, logout, profile)
|   +-- forms.py                # Custom registration form with email
|   +-- admin.py                # Profile admin registration
|   +-- apps.py                 # App configuration
|   +-- templates/users/
|       +-- register.html       # Registration page
|       +-- login.html          # Login page
|       +-- logout.html         # Logout confirmation
|       +-- profile.html        # User profile page
|       +-- create_profile.html # Profile creation form
|       +-- sellerprofile.html  # Public seller profile
|
+-- media/                      # User-uploaded files (product images, avatars)
+-- manage.py                   # Django management CLI
+-- package.json                # Node.js config for Tailwind CSS build
+-- LICENSE                     # All Rights Reserved -- Andrew Romany
```

---

## Models

### `product.product`
| Field         | Type                  | Description                        |
|---------------|-----------------------|------------------------------------|
| `seller_name` | ForeignKey(User)      | The user who listed the product    |
| `name`        | CharField(100)        | Product name                       |
| `price`       | IntegerField          | Product price in USD               |
| `description` | CharField(200)        | Short product description          |
| `specs`       | TextField             | Detailed product specifications    |
| `img`         | ImageField            | Product image                      |

### `product.Logo`
| Field  | Type       | Description               |
|--------|------------|---------------------------|
| `logo` | ImageField | Site logo image           |

### `User.Profile`
| Field         | Type                  | Description                        |
|---------------|-----------------------|------------------------------------|
| `user`        | OneToOneField(User)   | Associated Django user             |
| `img`         | ImageField            | Profile picture                    |
| `phonenumber` | CharField(100)        | Contact phone number               |

---

## URL Endpoints

### Product Routes (`/`)

| URL Pattern               | View                         | Name             | Description               |
|---------------------------|------------------------------|------------------|---------------------------|
| `/`                       | `products` (FBV)             | `products`       | Homepage / product listing |
| `/dets/<int:pk>/`         | `ProductClassBasedDetailView`| `product_detail` | Product detail page        |
| `/add/`                   | `AddClassBasedView`          | `AddProduct`     | Add new product            |
| `/update/<int:pk>/`       | `UpdateClassBasedView`       | `UpdateProduct`  | Update existing product    |
| `/delete/<int:pk>/`       | `DeleteClassBasedView`       | `DeleteProduct`  | Delete a product           |
| `/mylistings`             | `my_listings` (FBV)          | `mylistings`     | Seller's listings dashboard|

### User Routes (`/user/`)

| URL Pattern                    | View                     | Name             | Description                |
|--------------------------------|--------------------------|------------------|----------------------------|
| `/user/register/`              | `register`               | `register`       | User registration          |
| `/user/login/`                 | `LoginView` (Django)     | `login`          | User login                 |
| `/user/logout/`                | `LogoutView` (Django)    | `logout`         | User logout                |
| `/user/profile/`               | `profile`                | `profile`        | Current user's profile     |
| `/user/create_profile/`        | `create_profile`         | `create_profile` | Create user profile        |
| `/user/sellerprofile/<int:id>/`| `sellerprofile`          | `sellerprofile`  | Public seller profile      |

### Admin

| URL Pattern | Description                |
|-------------|----------------------------|
| `/admin/`   | Django admin dashboard     |

---

## Getting Started

### Prerequisites

- Python 3.9+
- Node.js (for Tailwind CSS compilation)
- pip

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/Andro0o0o0w-Romany/Django-ecommerce-1.git
   cd Django-ecommerce-1
   ```

2. **Create and activate a virtual environment**
   ```bash
   python -m venv env
   source env/bin/activate        # Linux/macOS
   env\Scripts\activate           # Windows
   ```

3. **Install Python dependencies**
   ```bash
   pip install django Pillow
   ```

4. **Install Node.js dependencies (for Tailwind CSS)**
   ```bash
   npm install
   ```

5. **Build Tailwind CSS**
   ```bash
   npm run build
   ```

6. **Run database migrations**
   ```bash
   python manage.py migrate
   ```

7. **Create a superuser (for admin access)**
   ```bash
   python manage.py createsuperuser
   ```

8. **Upload a site logo**
   - Log in to `/admin/`
   - Add a `Logo` object with your desired site logo image

9. **Run the development server**
   ```bash
   python manage.py runserver
   ```

10. **Open in browser**
    ```
    http://127.0.0.1:8000/
    ```

---

## Configuration

Key settings in `TrustBuy/settings.py`:

| Setting                | Value                      | Description                              |
|------------------------|----------------------------|------------------------------------------|
| `DEBUG`                | `True`                     | Set to `False` in production             |
| `DATABASES`            | SQLite3                    | Switch to PostgreSQL/MySQL for production|
| `MEDIA_ROOT`           | `<BASE_DIR>/media`         | Where uploaded files are stored          |
| `MEDIA_URL`            | `/media/`                  | URL prefix for media files               |
| `LOGIN_REDIRECT_URL`   | `product:products`         | Redirect after login                     |
| `LOGIN_URL`            | `User:login`               | Redirect for `@login_required`           |

---

## Screenshots Workflow

A typical user journey through TrustBuy:

1. **Browse** -- Visit the homepage to see all listed products in a responsive grid
2. **Search** -- Use the search bar to filter products by name
3. **Register** -- Create an account with email, username, and password
4. **Login** -- Sign in to access seller features
5. **Create Profile** -- Upload a profile picture and add a phone number
6. **Add Product** -- List a new product with name, price, description, specs, and image
7. **Manage Listings** -- View, edit, or delete your products from "My Listings"
8. **Product Details** -- Click on any product to view full details and seller info
9. **Purchase** -- Use the PayPal button on the product detail page to complete a purchase
10. **Seller Profile** -- Click on a seller's name to view their public profile

---

## License

**All Rights Reserved** -- Copyright (c) 2026 Andrew Romany.

This software is proprietary. No part of this project may be used, copied, modified, or distributed without prior written permission from the copyright holder. See the [LICENSE](LICENSE) file for full details.
