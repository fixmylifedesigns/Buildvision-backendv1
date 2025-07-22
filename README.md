# Buildvision-backendv1

🏗️ Architecture Platform Backend – README

📘 Overview

This backend supports a B2B platform where architecture firms can:

Manage user accounts and roles

Create and manage customer organizations

Upload and generate project documents, 3D models, and images

Render geospatial data on CesiumJS


The platform integrates with OpenAI (for image generation) and Meshy (for 3D generation).


---

📂 Database Schema (PostgreSQL)

users

id UUID PRIMARY KEY
email TEXT UNIQUE
password_hash TEXT
name TEXT
role TEXT CHECK(role IN ('admin', 'employee', 'customer_viewer'))
organization_id UUID
created_at TIMESTAMP

organizations

id UUID PRIMARY KEY
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
source_type TEXT CHECK(source_type IN ('text

https://chatgpt.com/share/687f33cc-2ef0-800b-b2ac-958a4badfec6
