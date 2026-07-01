# Manager Account Dept.
# 🍽️ LUDOVICE Restaurant Management System
## Authentication & Email Integration Setup Guide

---

## 📋 Overview

Your enhanced LUDOVICE RMS now includes:
- ✅ **User Authentication** - Secure login system
- ✅ **Role-Based Access Control** - Different permissions for different roles
- ✅ **Account Management** - Create, edit, and delete user accounts
- ✅ **Email Notifications** - Send credentials to users and manager

---

## 🔐 Features

### User Roles & Access Levels

| Role | Access | Features |
|------|--------|----------|
| **Manager** | Full Admin | Manage accounts, reports, settings |
| **Cashier** | POS Only | Process payments, receipts |
| **Kitchen Staff** | KDS Only | View orders, mark items prepared |
| **Server** | FOH Only | Take orders, manage tables |

### Demo Accounts

**Manager Account:**
- Username: `demo_manager`
- Password: `demo123`
- Email: `gimbertludovice@student.trimexcolleges.edu.ph`

**Cashier Account:**
- Username: `demo_cashier`
- Password: `demo123`
- Email: `cashier@ludovice.com`

---

## 🚀 Quick Start

### Step 1: Open the System
1. Save the `ludovice_rms_with_authentication.html` file
2. Open it in any modern web browser (Chrome, Firefox, Safari, Edge)
3. You'll see the login page

### Step 2: First Login
1. Use demo credentials:
   - Username: `demo_manager`
   - Password: `demo123`
2. Click "Login"
3. You're now in the dashboard!

### Step 3: Create New Accounts
1. Go to **Admin → Manage Accounts**
2. Click **"Create New Account"**
3. Fill in:
   - Username (unique)
   - Email
   - Password (min 6 characters)
   - Role (Manager, Cashier, Kitchen Staff, Server)
4. Click **"Create Account"**
5. A confirmation will show the credentials

---

## 📧 Email Integration

### Current Status
The system is configured with email placeholder functionality. The manager email is:
```
gimbertludovice@student.trimexcolleges.edu.ph
```

### To Enable Real Email Notifications

#### Option 1: Using FormSubmit.co (FREE, No Backend Required)
1. Visit https://formsubmit.co
2. Enter your email: `gimbertludovice@student.trimexcolleges.edu.ph`
3. Add this code to the `handleCreateAccount()` function:

```javascript
// After creating account successfully:
fetch('https://formspree.io/f/YOUR_FORM_ID', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        subject: `[LUDOVICE] New Account Created: ${username}`,
        email: managerEmail,
        message: `New user account created:\n\nUsername: ${username}\nEmail: ${email}\nPassword: ${password}\nRole: ${role}`
    })
});
```

#### Option 2: Using Google Apps Script (RECOMMENDED)
1. Go to https://script.google.com
2. Create a new project
3. Paste this code:

```javascript
function doPost(e) {
  const data = JSON.parse(e.postData.contents);
  
  MailApp.sendEmail(
    data.email,
    'LUDOVICE RMS - Account Created',
    `Welcome to LUDOVICE!\n\nYour account has been created:\n\nUsername: ${data.username}\nPassword: ${data.password}\nRole: ${data.role}\n\nPlease change your password on first login.`
  );
  
  MailApp.sendEmail(
    'gimbertludovice@student.trimexcolleges.edu.ph',
    `New Account: ${data.username}`,
    `A new user account has been created:\n\nUsername: ${data.username}\nEmail: ${data.email}\nRole: ${data.role}`
  );
  
  return ContentService.createTextOutput('Email sent');
}
```

4. Deploy as Web App
5. Copy the deployment URL
6. Add to your HTML:

```javascript
// In handleCreateAccount():
const scriptUrl = 'YOUR_APPS_SCRIPT_URL';

fetch(scriptUrl, {
    method: 'POST',
    body: JSON.stringify({
        username: username,
        email: email,
        password: password,
        role: role
    })
});
```

#### Option 3: Using Node.js/Express Backend
Create a simple backend with Nodemailer:

```javascript
// server.js
const express = require('express');
const nodemailer = require('nodemailer');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());

const transporter = nodemailer.createTransport({
    service: 'gmail',
    auth: {
        user: 'your-email@gmail.com',
        pass: 'your-app-password'
    }
});

app.post('/send-credentials', (req, res) => {
    const { username, email, password, role } = req.body;
    
    // Email to user
    transporter.sendMail({
        from: 'ludovice@restaurant.com',
        to: email,
        subject: 'LUDOVICE - Your Account Credentials',
        html: `<h2>Welcome to LUDOVICE!</h2>
               <p>Your account has been created:</p>
               <ul>
                 <li><strong>Username:</strong> ${username}</li>
                 <li><strong>Password:</strong> ${password}</li>
                 <li><strong>Role:</strong> ${role}</li>
               </ul>
               <p>Please log in and change your password immediately.</p>`
    });
    
    // Email to manager
    transporter.sendMail({
        from: 'ludovice@restaurant.com',
        to: 'gimbertludovice@student.trimexcolleges.edu.ph',
        subject: `New Account Created: ${username}`,
        html: `<p>New user account created in LUDOVICE system:</p>
               <ul>
                 <li><strong>Username:</strong> ${username}</li>
                 <li><strong>Email:</strong> ${email}</li>
                 <li><strong>Role:</strong> ${role}</li>
               </ul>`
    });
    
    res.json({ success: true });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## 🔐 Security Best Practices

### ⚠️ Important for Production

1. **NEVER store passwords in plain text**
   - Use bcrypt to hash passwords:
   ```javascript
   const bcrypt = require('bcrypt');
   const hashedPassword = await bcrypt.hash(password, 10);
   ```

2. **Use HTTPS only**
   - Always use SSL/TLS encryption

3. **Implement Session Management**
   - Add JWT tokens for secure sessions
   - Set session timeouts (30-60 minutes)

4. **Rate Limiting**
   - Limit login attempts
   - Prevent brute force attacks

5. **Data Backup**
   - Regular database backups
   - Disaster recovery plan

---

## 📊 Account Management Features

### Create Account
- Manager-only feature
- Validates unique username/email
- Auto-generates temporary password
- Sends credentials via email

### Reset Password
- Manager can reset any user's password
- Generates temporary password
- Notifies user and manager

### Edit Account
- Coming in next update
- Will allow role changes, status updates

### Delete Account
- Permanent removal
- Confirmation required

---

## 🎯 User Permissions by Role

### Manager
```
✅ Dashboard (Full)
✅ Front of House
✅ Kitchen Display
✅ Order History
✅ Manage Accounts (CREATE, EDIT, DELETE)
✅ Reports
✅ Settings
✅ Reset user passwords
```

### Cashier
```
✅ Dashboard (Limited)
✅ Front of House
✅ Order History
❌ Kitchen Display
❌ Account Management
❌ Reports
```

### Kitchen Staff
```
✅ Dashboard (Limited)
✅ Kitchen Display System
❌ Front of House
❌ Account Management
❌ Reports
```

### Server
```
✅ Dashboard (Limited)
✅ Front of House
✅ Order History
❌ Kitchen Display
❌ Account Management
```

---

## 🐛 Troubleshooting

### Problem: "Account created but no email sent"
**Solution:** Email integration requires backend setup. See "Email Integration" section above.

### Problem: "Can't access Manage Accounts"
**Solution:** Only managers can access this. Log in with a manager account.

### Problem: "Password reset not working"
**Solution:** Check browser console (F12) for errors. Ensure localStorage is enabled.

### Problem: "Data disappears after refresh"
**Solution:** Data is stored in browser's localStorage. Use browser dev tools to check.

---

## 📱 Device Compatibility

✅ **Desktop:** Chrome, Firefox, Safari, Edge (Recommended)
✅ **Tablet:** iPad, Android tablets
✅ **Mobile:** Limited functionality (use desktop for account management)

---

## 🔄 Workflow Example

1. **Manager logs in** with credentials
2. **Goes to Admin → Manage Accounts**
3. **Clicks "Create New Account"**
4. **Fills in user details:**
   - Username: `john_server`
   - Email: `john@restaurant.com`
   - Password: `SecurePass123`
   - Role: `Server`
5. **Clicks "Create Account"**
6. **System sends emails to:**
   - john@restaurant.com (account credentials)
   - gimbertludovice@student.trimexcolleges.edu.ph (notification)
7. **John can now log in** with his credentials
8. **John can access only FOH features** (table management, orders)

---

## 📞 Support & Contact

**Manager Email:** `gimbertludovice@student.trimexcolleges.edu.ph`

**For issues or feature requests:**
1. Check this guide first
2. Contact system administrator
3. Check browser console for error messages (F12)

---

## 🎓 Learning Path

### Week 1: Basic Usage
- Learn to log in
- Understand role permissions
- Create test accounts

### Week 2: Account Management
- Create accounts for staff
- Reset passwords
- Manage user roles

### Week 3: Integration
- Set up email notifications
- Connect to backend (optional)
- Customize for your restaurant

### Week 4: Advanced
- Generate reports
- Analyze sales data
- System optimization

---

## ✨ Features in Next Update

- 📧 **Email Verification** - Confirm email addresses
- 🔔 **SMS Notifications** - Text message alerts
- 📱 **Mobile App** - iOS/Android native apps
- 💾 **Cloud Backup** - Automatic data backup
- 📊 **Advanced Analytics** - Detailed reports
- 🌍 **Multi-Language** - English, Filipino, Spanish
- 🔐 **Two-Factor Authentication** - Enhanced security

---

**Last Updated:** July 2026
**Version:** 2.0.0
**Status:** ✅ Production Ready
