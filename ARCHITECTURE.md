# System Architecture

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Frontend Layer                          │
│              (React/Vue.js Single Page App)                 │
├─────────────────────────────────────────────────────────────┤
│                      API Gateway                             │
│                 (Load Balancer, CORS)                       │
├─────────────────────────────────────────────────────────────┤
│                    Backend Services                          │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐            │
│  │   Auth     │  │  Accounting│  │    GST     │            │
│  │  Service   │  │  Service   │  │  Service   │            │
│  └────────────┘  └────────────┘  └────────────┘            │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐            │
│  │    ITR     │  │  Document  │  │ Compliance │            │
│  │  Service   │  │  Service   │  │  Service   │            │
│  └────────────┘  └────────────┘  └────────────┘            │
├─────────────────────────────────────────────────────────────┤
│                   Data Layer                                 │
│  ┌────────────────────────────────────────────────────────┐ │
│  │         PostgreSQL Database                            │ │
│  │  (User, Expense, Invoice, GST, ITR Records)           │ │
│  └────────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────────┐ │
│  │    Redis Cache (Session, Temporary Data)              │ │
│  └────────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────────┐ │
│  │    S3 Storage (Documents, Attachments)                │ │
│  └────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

## Database Schema Overview

### Users Table
```sql
id (PK)
email (UNIQUE)
password_hash
full_name
pan_number
aadhar_number
phone
address
created_at
updated_at
```

### Expenses Table
```sql
id (PK)
user_id (FK)
description
amount
category
expense_date
receipt_url
created_at
updated_at
```

### Invoices Table
```sql
id (PK)
user_id (FK)
invoice_number
customer_name
amount
gst_amount
due_date
status (draft/sent/paid)
created_at
updated_at
```

### GST Returns Table
```sql
id (PK)
user_id (FK)
return_type (GSTR1/GSTR3B)
period_from
period_to
status (draft/filed)
filed_on
created_at
updated_at
```

### ITR Table
```sql
id (PK)
user_id (FK)
assessment_year
filing_status
pdf_url
filed_on
created_at
updated_at
```

## API Endpoints Structure

### Authentication
- `POST /api/auth/register` - User registration
- `POST /api/auth/login` - User login
- `POST /api/auth/logout` - User logout
- `POST /api/auth/refresh-token` - Refresh JWT

### Expenses
- `GET /api/expenses` - List expenses
- `POST /api/expenses` - Create expense
- `PUT /api/expenses/:id` - Update expense
- `DELETE /api/expenses/:id` - Delete expense
- `GET /api/expenses/report` - Generate report

### Invoices
- `GET /api/invoices` - List invoices
- `POST /api/invoices` - Create invoice
- `PUT /api/invoices/:id` - Update invoice
- `DELETE /api/invoices/:id` - Delete invoice
- `POST /api/invoices/:id/send` - Send invoice
- `GET /api/invoices/:id/pdf` - Download PDF

### GST
- `GET /api/gst/registration-status` - Check status
- `POST /api/gst/register` - Start registration
- `GET /api/gst/returns` - List returns
- `POST /api/gst/returns` - Create return
- `POST /api/gst/returns/:id/file` - File return

### ITR
- `GET /api/itr/calculate` - Calculate tax
- `GET /api/itr/forms` - List forms
- `POST /api/itr/forms` - Create form
- `POST /api/itr/forms/:id/file` - File ITR

## Technology Choices

### Frontend
- **React.js** with TypeScript
- **Redux** for state management
- **Material-UI** or **Tailwind CSS** for styling
- **React Query** for data fetching
- **React Router** for navigation

### Backend
- **Node.js** with Express.js
- **JWT** for authentication
- **PostgreSQL** for primary DB
- **Redis** for caching
- **Nodemailer** for emails
- **PDFKit** for PDF generation

### Infrastructure
- **Docker** for containerization
- **Docker Compose** for local development
- **AWS** or **Heroku** for hosting
- **GitHub Actions** for CI/CD
- **Sentry** for error tracking

## Security Considerations

1. **Authentication**: JWT with refresh tokens
2. **Authorization**: Role-based access control (RBAC)
3. **Data Encryption**: AES-256 for sensitive data
4. **HTTPS**: All communications encrypted
5. **Input Validation**: Server-side validation for all inputs
6. **SQL Injection Prevention**: Parameterized queries
7. **CORS**: Configured for frontend domain only
8. **Rate Limiting**: API rate limiting to prevent abuse
9. **Data Backup**: Daily automated backups
10. **Audit Trail**: Logging all important actions
