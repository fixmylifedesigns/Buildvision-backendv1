# Buildvision-backendv1

🏗️ Architecture Platform Backend – README

📘 Overview

This backend supports a B2B platform where architecture firms can:

Manage user accounts and roles within their firm

Create and manage customer organizations (clients)

Upload and generate project documents, 3D models, and images

Render geospatial data on CesiumJS


The platform integrates with OpenAI (for image generation) and Meshy (for 3D generation).


---

🏢 Multi-Tenant Structure

Firm (Architecture Company)
 └── Users (staff: admin, employee)
 └── Organizations (clients)
      └── Projects
           └── Documents / Images / Models


---

📂 Database Schema (PostgreSQL)

firms

id UUID PRIMARY KEY
name TEXT
industry TEXT
country TEXT
created_at TIMESTAMP

users

id UUID PRIMARY KEY
email TEXT UNIQUE
password_hash TEXT
name TEXT
role TEXT CHECK(role IN ('admin', 'employee'))
firm_id UUID REFERENCES firms(id)
created_at TIMESTAMP

organizations

id UUID PRIMARY KEY
firm_id UUID REFERENCES firms(id)
name TEXT
logo_url TEXT
website TEXT
phone TEXT
lead_contact_name TEXT
lead_contact_email TEXT

customer_contacts

id UUID PRIMARY KEY
organization_id UUID REFERENCES organizations(id)
name TEXT
email TEXT
role TEXT DEFAULT 'viewer'
can_upload BOOLEAN DEFAULT true
can_delete BOOLEAN DEFAULT false

projects

id UUID PRIMARY KEY
organization_id UUID REFERENCES organizations(id)
name TEXT
description TEXT
created_by UUID REFERENCES users(id)
latitude DOUBLE PRECISION
longitude DOUBLE PRECISION
height DOUBLE PRECISION DEFAULT 0
created_at TIMESTAMP

documents

id UUID PRIMARY KEY
project_id UUID REFERENCES projects(id)
uploaded_by UUID REFERENCES users(id)
file_url TEXT
file_type TEXT
visibility TEXT DEFAULT 'private'
created_at TIMESTAMP

images

id UUID PRIMARY KEY
project_id UUID REFERENCES projects(id)
uploaded_by UUID REFERENCES users(id)
prompt TEXT
image_url TEXT
source_type TEXT CHECK(source_type IN ('text', 'image', 'manual'))
tag TEXT[]
created_at TIMESTAMP

models

id UUID PRIMARY KEY
project_id UUID REFERENCES projects(id)
generated_by UUID REFERENCES users(id)
prompt TEXT
model_url TEXT
thumbnail_url TEXT
format TEXT DEFAULT 'glb'
source_type TEXT CHECK(source_type IN ('text', 'image', 'manual'))
lat DOUBLE PRECISION
lon DOUBLE PRECISION
height DOUBLE PRECISION DEFAULT 0
scale DOUBLE PRECISION DEFAULT 1.0
rotation_x DOUBLE PRECISION DEFAULT 0
rotation_y DOUBLE PRECISION DEFAULT 0
rotation_z DOUBLE PRECISION DEFAULT 0
tag TEXT[]
status TEXT CHECK(status IN ('processing', 'complete', 'error')) DEFAULT 'complete'
created_at TIMESTAMP


---

🚀 API Endpoints

🔐 Auth

POST /auth/register – Register a new firm and admin user

POST /auth/login

GET /auth/me


🧑‍💼 Users

POST /users – Create employee under firm

PATCH /users/:id

GET /users?firm_id=xxx


🏢 Firms & Organizations

POST /organizations – Add customer organization under firm

GET /organizations/:id

GET /organizations?firm_id=xxx

PATCH /organizations/:id


👥 Customer Contacts

POST /contacts

GET /contacts?organization_id=xxx


📁 Projects

POST /projects

GET /projects/:id

GET /projects?organization_id=xxx


📂 Documents

POST /projects/:id/documents

GET /projects/:id/documents


🖼️ Image Generation / Upload

POST /projects/:id/images/generate – OpenAI text-to-image or image-to-image

POST /projects/:id/images/upload – manual upload with tag

GET /projects/:id/images


🧱 3D Model Generation / Upload

POST /projects/:id/models/generate – Meshy text-to-3D or image-to-3D

POST /projects/:id/models/upload – manual upload with Cesium positioning and tag

GET /projects/:id/models


🏷️ Search with Tags (Optional)

GET /projects/:id/images?tag=concept

GET /projects/:id/models?tag=roof



---

🗺️ CesiumJS Integration Fields

For each model:

lat, lon, height → placed on globe

scale, rotation_x/y/z → control orientation and size


Each project has a global position (latitude, longitude, height) to act as anchor.


---

📦 Optional Add-Ons

Versioning for models/documents

Sharing settings / public preview

Export CesiumJS scenes

Webhooks for generation status



---

🔐 Role-Based Access Summary

Role	Can Upload	Can Delete	View Everything

admin	✅	✅	✅
employee	✅	✅	✅
customer_viewer	✅ (optional)	❌	✅ (limited)



---

Ready to build? Let’s code it step-by-step. Let me know if you want a full Express backend with file uploads and auth.

https://chatgpt.com/share/687f33cc-2ef0-800b-b2ac-958a4badfec6
