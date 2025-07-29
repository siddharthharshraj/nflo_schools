# 🛡️ NFLO School Module – Secure MERN Implementation Guide

## 📘 Overview

This guide outlines the full MERN implementation flow for the NFLO School Registration and Dashboard system, including:
- Secure JWT-based authentication
- Unique school refer code generation
- Student-to-school mapping via refer code
- Dashboard view with stats
- Data security best practices

---

## 🏫 School Registration Flow

### 📥 Input Fields:
- School Name
- School Affiliation Code (e.g., CBSE1234)
- School Mobile Number
- School Email
- School City with Pin-Code

### 🔐 Refer Code Generation Logic

```js
function generateSchoolReferCode(affiliationCode, schoolName, pinCode) {
  const aff = affiliationCode.slice(0, 4).toUpperCase();
  const name = schoolName.replace(/\s/g, '').slice(0, 4).toUpperCase();
  return aff + name + pinCode;
}
```

### ✅ Backend Registration Handler

- Check if email exists
- Generate referCode
- Check referCode uniqueness
- Save school with hashed password

---

## 🧠 MongoDB Models

### 🏫 School Model

```js
{
  name: String,
  affiliationCode: String,
  phone: String,
  email: String (unique),
  city: String,
  pinCode: String,
  referCode: String (unique),
  password: String (hashed)
}
```

### 👨‍🎓 Student Model

```js
{
  name: String,
  email: String,
  class: String,
  phone: String,
  schoolReferCode: String,
  paymentStatus: 'paid' | 'pending'
}
```

---

## 🔐 JWT Auth & Middleware

### 🔑 Login Endpoint

- Match email and password
- Return JWT with referCode

### 🔒 Middleware

```js
const token = req.headers.authorization.split(' ')[1];
const decoded = jwt.verify(token, process.env.JWT_SECRET);
req.school = await School.findById(decoded.id);
```

---

## 📊 Dashboard Endpoint

```js
GET /api/school/dashboard

Returns:
{
  totalStudents: Number,
  paid: Number,
  pending: Number,
  students: [ { name, email, class, status } ]
}
```

---

## 🔐 Data Security Best Practices

- All API routes protected via JWT middleware
- Use HTTPS for all frontend/backend connections
- Store passwords with bcrypt (never plaintext)
- Limit returned data fields (omit PII like password)
- Validate all inputs (emails, phone, class)
- Use helmet, CORS, rate-limit on Express server
- Use .env to store DB credentials, secrets
- Allow only known origins via CORS

---

## ✅ Edge Cases & Validations

| Case | Handling |
|------|----------|
| Duplicate email | Block and show “email already registered” |
| Duplicate refer code | Already Registered : Contact : support-nflo@safefintech.in |
| Invalid email format | Reject |
| Empty required fields | Reject |
| Invalid phone/pin | Add regex validation |
| Unauthorized dashboard access | 401 Unauthorized |
| Token tampering | 403 Forbidden |
| Searching non-existent student | Return empty array | No student of Such Name exists or registered yet |
| Multiple students with same name | Return all matches with payment status |

---

## 📁 Recommended Folder Structure

```
server/
├── models/
├── routes/
├── controllers/
├── middleware/
└── server.js

client/
├── src/
│   ├── components/
│   ├── context/
│   └── App.js
```

---

## 🧪 Recommended Tools

- Postman for API testing
- MongoDB Atlas for DB hosting
- Render for backend, Vercel for frontend
- Nodemailer for email setup (future)

---

**Built for NFLO 2025 – Secure & Scalable School Module**---

## 📢 Student Dashboard Notice

A constant **banner/message** should be shown at the top of the **Student Dashboard**:

   > "_Ask your students to register using your School Refer Code: `XXXXXX` for payment tracking and result access._"


This banner should be dynamically populated based on the school refer code (if known), and should appear before registration or payment.

---

## 🛡️ Legal Compliance & Data Protection

All student and school data is:
- ✅ Stored securely in encrypted MongoDB collections (PII protected)
- ✅ Accessed only via authenticated JWT sessions
- ✅ Never exposed to the frontend or third parties
- ✅ Compliant with the **Data Protection Agreement (DPA)** and **Indian Data Privacy Laws (IT Act, 2000 + amendments)**
- ✅ Not shared with any unauthorized party, including marketing or affiliates

SAFE Fintech is the sole custodian of this data and is responsible for its protection under Indian law.

---


# 🎯 NFLO Business Flow Summary – School Registration & Dashboard

## Step 1: School Registration

Schools **self-register** on the NFLO portal using a form with the following fields:
- **School Name**  
- **Affiliation Code** (e.g., CBSE012345)  
- **Mobile Number**  
- **Official Email ID**  
- **City & Pin Code**

---

## Step 2: Automatic Refer Code Generation

After successful registration, each school is assigned a **unique refer code** based on the following logic:

```
Refer Code = First 4 letters of Affiliation Code +
             First 4 letters of School Name (no spaces) +
             Full Pin Code
```

### 🔐 Example:
- Affiliation Code: `CBSE012345`  
- School Name: `DAV Public School`  
- Pin Code: `800001`  
➡️ **Refer Code**: `CBSEDAVP800001`

---

## Step 3: Email Confirmation

The school receives a confirmation email containing:
- Registration acknowledgement
- Their unique school refer code

---

## Step 4: Student Registration Using Refer Code

- Students will enter the **refer code** provided by their school at the time of registration/payment.  
- This links the student to the respective school in the backend.

---

## Step 5: School Dashboard Features

Upon logging in, schools will have access to:
1. **Total number of students** registered via their refer code  
2. **Student-wise payment status:**
   - ✅ Paid  
   - ❌ Pending  
3. **Search bar** to filter students by name or email  
4. A **permanent banner message** at the top:
   > "_Ask your students to register using your School Refer Code: `XXXXXX` for payment tracking and result access._"

---

## 🔐 Data Security & Compliance

- All school and student data is securely stored in **MongoDB on AWS**
- JWT tokens are used to protect login and dashboard access
- PII (Personally Identifiable Information) is **never exposed to the frontend**
- The system is fully **compliant with India’s Data Protection Agreement (DPA)** and **IT Act (2000 + Amendments)**
- All APIs should run over HTTPS with TLS 1.2+
- Maintain logs of data access and login attempts for traceability
- Explicitly obtain and log consent from schools/students for storing their data

SAFE Fintech is the sole data controller and custodian under this policy.

---
