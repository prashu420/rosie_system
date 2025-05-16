# Guru Link - Module Management System

A full-stack Next.js application for managing educational modules and users. Admins can create modules with chapters and assign them to users/trainees. Users can view and access their assigned modules through an intuitive dashboard interface.

## Features

- **Authentication**: Secure login for both admin and user roles using NextAuth
- **Module Management**: Create, edit, and delete modules with rich content chapters
- **User Management**: Create, edit, and delete users with role-based permissions
- **Module Assignment**: Assign modules to individual users or in batch
- **Interactive Dashboard**: View statistics, progress, and manage content with animated UI
- **Responsive Design**: Works seamlessly on desktop and mobile devices

## Tech Stack

- **Frontend**: Next.js 15.2.3 with React Server Components, TypeScript, Tailwind CSS
- **Backend**: Next.js API Routes, PostgreSQL
- **Authentication**: NextAuth.js with role-based access control
- **Form Validation**: React Hook Form with Zod schema validation
- **UI Components**: Custom components with Framer Motion animations
- **State Management**: React Context API and Server Actions

## Project Workflow

### Admin Workflow

1. **Login**: Admins log in using their credentials
2. **Dashboard**: View system statistics (users, modules, assignments)
3. **Module Management**:
   - Create new modules with title, description, and chapters
   - Edit existing modules
   - Delete modules when no longer needed
4. **User Management**:
   - Create new users/trainees
   - Edit user information
   - Delete users when necessary
5. **Module Assignment**:
   - Assign modules to individual users
   - Batch assign modules to multiple users
   - View and manage existing assignments

### User/Trainee Workflow

1. **Login**: Users log in with credentials provided by admin
2. **Dashboard**: View assigned modules with progress indicators
3. **Module Access**: View and interact with assigned module content
4. **Progress Tracking**: Automatic tracking of module completion

## Database Schema



-- Create admins table (plain password)
CREATE TABLE admins (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,  -- Plain text password
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create users table (plain password)
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,  -- Plain text password
    created_by INTEGER REFERENCES admins(id) ON DELETE SET NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create modules table (chapters with id in JSONB)
CREATE TABLE modules (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    chapters JSONB NOT NULL DEFAULT '[]',  -- Array of objects, e.g., [{"id": 1, "title": "Chapter 1"}, ...]
    created_by INTEGER REFERENCES admins(id) ON DELETE SET NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create user_modules table (unchanged)
CREATE TABLE user_modules (
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    module_id INTEGER REFERENCES modules(id) ON DELETE CASCADE,
    assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (user_id, module_id)
);

## Project Structure

```
├── public/              # Static assets
├── src/                 # Application source code
│   ├── app/             # Next.js app directory
│   │   ├── admin/       # Admin routes
│   │   ├── api/         # API routes
│   │   ├── dashboard/   # User dashboard routes
│   │   ├── login/       # Authentication routes
│   ├── components/      # React components
│   │   ├── admin/       # Admin-specific components
│   │   ├── auth/        # Authentication components
│   │   ├── dashboard/   # Dashboard components
│   │   ├── layout/      # Layout components
│   │   ├── ui/          # UI components
│   ├── lib/             # Utility functions
│   ├── types/           # TypeScript type definitions
├── .env                 # Environment variables
├── schema.sql           # Database schema
```

## Setup Instructions

1. **Clone the repository**

```bash
git clone https://github.com/prashu420/rosie_system.git
cd rosie_system
```

2. **Install dependencies**

```bash
npm install
```

3. **Configure environment variables**

Create a `.env` file in the root directory with your PostgreSQL credentials:

```env
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=guru_link
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_password

NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your_nextauth_secret
```

4. **Set up the database**

- Create a PostgreSQL database named `guru_link`
- Run the schema.sql file to create the necessary tables
- Insert an initial admin user:

```sql
INSERT INTO users (username, email, password, role) 
VALUES ('admin', 'admin@example.com', 'password', 'admin');
```

5. **Run the development server**

```bash
npm run dev
```

6. **Access the application**

Open [http://localhost:3000](http://localhost:3000) in your browser.

- Admin login: `admin@example.com` with password `password`
- For user login, create users through the admin interface first

## Deployment

The application can be deployed to any platform that supports Next.js applications, such as Vercel, Netlify, or a custom server.

```bash
npm run build
npm start
```

## Security Note

This application uses plain text passwords for simplicity in the demonstration. In a production environment, you should implement proper password hashing using bcrypt or a similar library, enable HTTPS, and follow other security best practices.
