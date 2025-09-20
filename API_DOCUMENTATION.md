# GreatMinds Fintech API Documentation V2

## Table of Contents
1. [Authentication](#1-authentication)
2. [Admin Authentication](#2-admin-authentication)
3. [User Management](#3-user-management)
4. [Wallet Operations](#4-wallet-operations)
5. [Crypto Operations](#5-crypto-operations)
6. [Crypto Orders](#6-crypto-orders)
7. [Crypto Rates](#7-crypto-rates)
8. [Bill Payments](#8-bill-payments)
9. [Gift Cards](#9-gift-cards)
10. [Gift Card Buy](#10-gift-card-buy)
11. [Transaction Management](#11-transaction-management)
12. [Transaction PIN](#12-transaction-pin)
13. [Six Digit Password](#13-six-digit-password)
14. [Session Management](#14-session-management)
15. [KYC Management](#15-kyc-management)
16. [Notifications](#16-notifications)
17. [Support System](#17-support-system)
18. [Activity Logs](#18-activity-logs)
19. [Referral System](#19-referral-system)
20. [Promo Codes](#20-promo-codes)
21. [Cashback System](#21-cashback-system)
22. [Chat System](#22-chat-system)
23. [Dashboard](#23-dashboard)
24. [Settings](#24-settings)
25. [Disputes](#25-disputes)
26. [USD Rates](#26-usd-rates)
27. [Admin Panel](#27-admin-panel)
28. [Webhooks](#28-webhooks)
29. [Mudhaxk Wallet](#29-mudhaxk-wallet)

---

## Base URL
\`\`\`
Production: https://site--greatminds-ng-server--hc6gfyjh7dnv.code.run
Development: http://localhost:5000
\`\`\`

## Authentication Requirements Legend
- 🔓 **Public**: No authentication required
- 🔐 **User Auth**: Requires user authentication token
- 👑 **Admin Auth**: Requires admin authentication token
- 📱 **PIN Required**: Requires transaction PIN verification
- 🔒 **Biometric**: Requires biometric authentication
- 📋 **KYC Level**: Requires specific KYC verification level
- ⏰ **Session Timeout**: Has session timeout requirements

---

## 1. Authentication

### Base Route: `/api/auth`

#### 1.1 User Registration 🔓
\`\`\`http
POST /api/auth/signup
\`\`\`

**Request Body:**
\`\`\`json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "phone": "+2348012345678",
  "password": "SecurePass123!",
  "referralCode": "REF123456" // Optional
}
\`\`\`

**Validation Rules:**
- `firstName`: 2-50 characters, letters and spaces only
- `lastName`: 2-50 characters, letters and spaces only
- `email`: Valid email format
- `phone`: Nigerian phone number format (+234 or 0 followed by valid network codes)
- `password`: Minimum 8 characters with uppercase, lowercase, number, and special character
- `referralCode`: Optional, 6-10 characters

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Account created successfully. Please verify your email.",
  "data": {
    "user": {
      "id": "user_id",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "phone": "+2348012345678",
      "isEmailVerified": false,
      "isPhoneVerified": false,
      "kycLevel": "none"
    },
    "tokens": {
      "accessToken": "jwt_access_token",
      "refreshToken": "jwt_refresh_token"
    }
  }
}
\`\`\`

#### 1.2 User Login 🔓
\`\`\`http
POST /api/auth/login
\`\`\`

**Request Body:**
\`\`\`json
{
  "identifier": "john.doe@example.com", // Email or phone
  "password": "SecurePass123!",
  "twoFactorCode": "123456" // Optional, required if 2FA is enabled
}
\`\`\`

**Response (Without 2FA):**
\`\`\`json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": "user_id",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "phone": "+2348012345678",
      "isEmailVerified": true,
      "isPhoneVerified": true,
      "kycLevel": "basic",
      "has2FA": false,
      "hasTransactionPin": true,
      "hasSixDigitPassword": false
    },
    "tokens": {
      "accessToken": "jwt_access_token",
      "refreshToken": "jwt_refresh_token"
    }
  }
}
\`\`\`

**Response (With 2FA Required):**
\`\`\`json
{
  "success": true,
  "message": "Two-factor authentication required",
  "data": {
    "requiresTwoFactor": true,
    "tempToken": "temporary_token_for_2fa",
    "twoFactorMethod": "sms" // or "email" or "authenticator"
  }
}
\`\`\`

#### 1.3 Two-Factor Authentication Verification 🔓
\`\`\`http
POST /api/auth/2fa/verify
\`\`\`

**Request Body:**
\`\`\`json
{
  "tempToken": "temporary_token_from_login",
  "twoFactorCode": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Two-factor authentication successful",
  "data": {
    "user": {
      "id": "user_id",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "phone": "+2348012345678",
      "isEmailVerified": true,
      "isPhoneVerified": true,
      "kycLevel": "basic",
      "has2FA": true,
      "hasTransactionPin": true,
      "hasSixDigitPassword": false
    },
    "tokens": {
      "accessToken": "jwt_access_token",
      "refreshToken": "jwt_refresh_token"
    }
  }
}
\`\`\`

#### 1.4 Email Verification 🔓
\`\`\`http
POST /api/auth/send-email-verification
\`\`\`

**Request Body:**
\`\`\`json
{
  "email": "john.doe@example.com"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Verification code sent to your email",
  "data": {
    "expiresIn": 600 // 10 minutes in seconds
  }
}
\`\`\`

\`\`\`http
POST /api/auth/verify-email
\`\`\`

**Request Body:**
\`\`\`json
{
  "email": "john.doe@example.com",
  "otp": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Email verified successfully",
  "data": {
    "isEmailVerified": true
  }
}
\`\`\`

#### 1.5 Phone Verification 🔓
\`\`\`http
POST /api/auth/send-phone-verification
\`\`\`

**Request Body:**
\`\`\`json
{
  "phone": "+2348012345678"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Verification code sent to your phone",
  "data": {
    "expiresIn": 600 // 10 minutes in seconds
  }
}
\`\`\`

\`\`\`http
POST /api/auth/verify-phone
\`\`\`

**Request Body:**
\`\`\`json
{
  "phone": "+2348012345678",
  "otp": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Phone verified successfully",
  "data": {
    "isPhoneVerified": true
  }
}
\`\`\`

#### 1.6 Password Reset 🔓
\`\`\`http
POST /api/auth/forgot-password
\`\`\`

**Request Body:**
\`\`\`json
{
  "identifier": "john.doe@example.com" // Email or phone
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Password reset code sent",
  "data": {
    "expiresIn": 900 // 15 minutes in seconds
  }
}
\`\`\`

\`\`\`http
POST /api/auth/reset-password
\`\`\`

**Request Body:**
\`\`\`json
{
  "token": "reset_token_from_email_or_sms",
  "password": "NewSecurePass123!"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Password reset successfully",
  "data": {
    "passwordResetAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 1.7 Get User Profile 🔐
\`\`\`http
GET /api/auth/profile
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "phone": "+2348012345678",
      "isEmailVerified": true,
      "isPhoneVerified": true,
      "kycLevel": "basic",
      "has2FA": true,
      "hasTransactionPin": true,
      "hasSixDigitPassword": false,
      "createdAt": "2024-01-01T00:00:00Z",
      "lastLoginAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 1.8 Change Password 🔐
\`\`\`http
POST /api/auth/change-password
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currentPassword": "OldSecurePass123!",
  "newPassword": "NewSecurePass123!"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Password changed successfully",
  "data": {
    "passwordChangedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 1.9 Two-Factor Authentication Setup 🔐
\`\`\`http
POST /api/auth/2fa/setup
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "method": "sms" // "sms", "email", or "authenticator"
}
\`\`\`

**Response (SMS/Email):**
\`\`\`json
{
  "success": true,
  "message": "Verification code sent",
  "data": {
    "method": "sms",
    "expiresIn": 600
  }
}
\`\`\`

**Response (Authenticator):**
\`\`\`json
{
  "success": true,
  "message": "Scan QR code with your authenticator app",
  "data": {
    "method": "authenticator",
    "qrCode": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
    "secret": "JBSWY3DPEHPK3PXP",
    "backupCodes": ["12345678", "87654321", "11223344"]
  }
}
\`\`\`

#### 1.10 Enable Two-Factor Authentication 🔐
\`\`\`http
POST /api/auth/2fa/enable
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "verificationCode": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Two-factor authentication enabled successfully",
  "data": {
    "has2FA": true,
    "method": "sms",
    "enabledAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 1.11 Disable Two-Factor Authentication 🔐
\`\`\`http
POST /api/auth/2fa/disable
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "password": "SecurePass123!",
  "verificationCode": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Two-factor authentication disabled successfully",
  "data": {
    "has2FA": false,
    "disabledAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 1.12 Refresh Token 🔓
\`\`\`http
POST /api/auth/refresh-token
\`\`\`

**Request Body:**
\`\`\`json
{
  "refreshToken": "jwt_refresh_token"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Token refreshed successfully",
  "data": {
    "accessToken": "new_jwt_access_token",
    "refreshToken": "new_jwt_refresh_token"
  }
}
\`\`\`

#### 1.13 Logout 🔐
\`\`\`http
POST /api/auth/logout
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Logged out successfully"
}
\`\`\`

#### 1.14 Delete Account 🔐
\`\`\`http
DELETE /api/auth/account
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "password": "SecurePass123!",
  "reason": "No longer need the service" // Optional
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Account deletion initiated. You have 30 days to cancel this action.",
  "data": {
    "deletionScheduledFor": "2024-02-15T10:30:00Z"
  }
}
\`\`\`

---

## 2. Admin Authentication

### Base Route: `/api/auth-admin`

#### 2.1 Admin Login 🔓
\`\`\`http
POST /api/auth-admin/login
\`\`\`

**Request Body:**
\`\`\`json
{
  "email": "admin@greatminds.com",
  "password": "AdminSecurePass123!"
}
\`\`\`

**Response (Without 2FA):**
\`\`\`json
{
  "success": true,
  "message": "Admin login successful",
  "data": {
    "admin": {
      "id": "admin_id",
      "email": "admin@greatminds.com",
      "role": "Super Admin",
      "permissions": ["users:read", "users:write", "transactions:read"],
      "lastLoginAt": "2024-01-15T10:30:00Z"
    },
    "tokens": {
      "accessToken": "jwt_admin_access_token",
      "refreshToken": "jwt_admin_refresh_token"
    }
  }
}
\`\`\`

**Response (With 2FA Required):**
\`\`\`json
{
  "success": true,
  "message": "Two-factor authentication required",
  "data": {
    "requiresTwoFactor": true,
    "tempToken": "temporary_admin_token_for_2fa",
    "twoFactorMethod": "authenticator"
  }
}
\`\`\`

#### 2.2 Admin 2FA Verification 🔓
\`\`\`http
POST /api/auth-admin/2fa/verify
\`\`\`

**Request Body:**
\`\`\`json
{
  "tempToken": "temporary_admin_token_from_login",
  "twoFactorCode": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Admin two-factor authentication successful",
  "data": {
    "admin": {
      "id": "admin_id",
      "email": "admin@greatminds.com",
      "role": "Super Admin",
      "permissions": ["users:read", "users:write", "transactions:read"],
      "lastLoginAt": "2024-01-15T10:30:00Z"
    },
    "tokens": {
      "accessToken": "jwt_admin_access_token",
      "refreshToken": "jwt_admin_refresh_token"
    }
  }
}
\`\`\`

#### 2.3 Admin Refresh Token 👑
\`\`\`http
POST /api/auth-admin/refresh-token
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "refreshToken": "jwt_admin_refresh_token"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Admin token refreshed successfully",
  "data": {
    "accessToken": "new_jwt_admin_access_token",
    "refreshToken": "new_jwt_admin_refresh_token"
  }
}
\`\`\`

#### 2.4 Admin 2FA Setup 👑
\`\`\`http
POST /api/auth-admin/2fa/setup
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "method": "authenticator" // "authenticator", "sms", or "email"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Scan QR code with your authenticator app",
  "data": {
    "method": "authenticator",
    "qrCode": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
    "secret": "JBSWY3DPEHPK3PXP",
    "backupCodes": ["12345678", "87654321", "11223344"]
  }
}
\`\`\`

#### 2.5 Admin Change Password 👑
\`\`\`http
POST /api/auth-admin/change-password
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currentPassword": "OldAdminPass123!",
  "newPassword": "NewAdminPass123!"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Admin password changed successfully",
  "data": {
    "passwordChangedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 2.6 Admin IP Whitelist Management 👑
\`\`\`http
POST /api/auth-admin/whitelist/:adminId/ip
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "ip": "192.168.1.100",
  "description": "Office IP address"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "IP address added to whitelist",
  "data": {
    "ip": "192.168.1.100",
    "description": "Office IP address",
    "addedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

\`\`\`http
DELETE /api/auth-admin/whitelist/:adminId/ip/:ip
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "IP address removed from whitelist"
}
\`\`\`

---

## 3. User Management

### Base Route: `/api/user`

#### 3.1 Get User Profile 🔐
\`\`\`http
GET /api/user/profile
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "phone": "+2348012345678",
      "dateOfBirth": "1990-01-01",
      "gender": "male",
      "address": {
        "street": "123 Main Street",
        "city": "Lagos",
        "state": "Lagos",
        "postalCode": "100001",
        "country": "Nigeria"
      },
      "avatar": "https://example.com/avatar.jpg",
      "isEmailVerified": true,
      "isPhoneVerified": true,
      "kycLevel": "intermediate",
      "has2FA": true,
      "hasTransactionPin": true,
      "hasSixDigitPassword": false,
      "createdAt": "2024-01-01T00:00:00Z",
      "lastLoginAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 3.2 Update User Profile 🔐
\`\`\`http
PUT /api/user/profile
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
Content-Type: multipart/form-data
\`\`\`

**Request Body (Form Data):**
\`\`\`
firstName: John
lastName: Doe
dateOfBirth: 1990-01-01
gender: male
address[street]: 123 Main Street
address[city]: Lagos
address[state]: Lagos
address[postalCode]: 100001
avatar: [file upload]
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Profile updated successfully",
  "data": {
    "user": {
      "id": "user_id",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "phone": "+2348012345678",
      "dateOfBirth": "1990-01-01",
      "gender": "male",
      "address": {
        "street": "123 Main Street",
        "city": "Lagos",
        "state": "Lagos",
        "postalCode": "100001",
        "country": "Nigeria"
      },
      "avatar": "https://example.com/new-avatar.jpg",
      "updatedAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 3.3 Register Biometrics 🔐
\`\`\`http
POST /api/user/biometric/register
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "biometricType": "fingerprint", // "fingerprint", "face", or "voice"
  "biometricData": "base64_encoded_biometric_template",
  "deviceInfo": {
    "deviceId": "device_unique_id",
    "deviceName": "iPhone 12 Pro",
    "platform": "ios"
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Biometric registered successfully",
  "data": {
    "biometricId": "biometric_id",
    "biometricType": "fingerprint",
    "registeredAt": "2024-01-15T10:30:00Z",
    "deviceInfo": {
      "deviceId": "device_unique_id",
      "deviceName": "iPhone 12 Pro",
      "platform": "ios"
    }
  }
}
\`\`\`

#### 3.4 Submit KYC Documents 🔐
\`\`\`http
POST /api/user/kyc
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
Content-Type: multipart/form-data
\`\`\`

**Request Body (Form Data):**
\`\`\`
idDocument: [file upload] // Government issued ID
proofOfAddress: [file upload] // Utility bill or bank statement
selfie: [file upload] // Selfie with ID document
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "KYC documents submitted successfully",
  "data": {
    "kycSubmissionId": "kyc_submission_id",
    "status": "pending_review",
    "submittedAt": "2024-01-15T10:30:00Z",
    "estimatedReviewTime": "24-48 hours"
  }
}
\`\`\`

#### 3.5 Get KYC Status 🔐
\`\`\`http
GET /api/user/kyc/status
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "kycLevel": "intermediate",
    "status": "approved",
    "submittedAt": "2024-01-15T10:30:00Z",
    "reviewedAt": "2024-01-16T14:20:00Z",
    "reviewedBy": "admin_id",
    "documents": {
      "idDocument": {
        "status": "approved",
        "type": "national_id",
        "number": "****1234"
      },
      "proofOfAddress": {
        "status": "approved",
        "type": "utility_bill"
      },
      "selfie": {
        "status": "approved"
      }
    },
    "limits": {
      "dailyTransactionLimit": 1000000,
      "monthlyTransactionLimit": 10000000,
      "cryptoTradingEnabled": true,
      "internationalTransfersEnabled": true
    }
  }
}
\`\`\`

#### 3.6 Delete Account 🔐
\`\`\`http
DELETE /api/user/delete
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "password": "SecurePass123!",
  "reason": "No longer need the service",
  "confirmDeletion": true
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Account deletion initiated. You have 30 days to cancel this action.",
  "data": {
    "deletionScheduledFor": "2024-02-15T10:30:00Z",
    "cancellationDeadline": "2024-02-14T10:30:00Z"
  }
}
\`\`\`

---

## 4. Wallet Operations

### Base Route: `/api/wallet`

#### 4.1 Get Wallet Overview 🔐 ⏰
\`\`\`http
GET /api/wallet
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "wallet": {
      "userId": "user_id",
      "nairaBalance": 150000.50,
      "bonusBalance": 5000.00,
      "totalBalance": 155000.50,
      "cryptoWallets": [
        {
          "currency": "BTC",
          "balance": 0.00125000,
          "usdValue": 52.50,
          "nairaValue": 78750.00
        },
        {
          "currency": "ETH",
          "balance": 0.05000000,
          "usdValue": 125.00,
          "nairaValue": 187500.00
        }
      ],
      "lastUpdated": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 4.2 Get Wallet Summary 🔐 ⏰
\`\`\`http
GET /api/wallet/summary
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "summary": {
      "totalNairaBalance": 150000.50,
      "totalBonusBalance": 5000.00,
      "totalCryptoValueNaira": 266250.00,
      "totalPortfolioValue": 421250.50,
      "monthlyInflow": 500000.00,
      "monthlyOutflow": 350000.00,
      "transactionCount": {
        "today": 5,
        "thisWeek": 23,
        "thisMonth": 87
      },
      "topCrypto": {
        "currency": "ETH",
        "balance": 0.05000000,
        "percentage": 70.5
      }
    }
  }
}
\`\`\`

#### 4.3 Get Naira Balance 🔐 ⏰
\`\`\`http
GET /api/wallet/naira
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "nairaBalance": 150000.50,
    "availableBalance": 148000.50,
    "pendingBalance": 2000.00,
    "lastUpdated": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 4.4 Get Bonus Balance 🔐 ⏰
\`\`\`http
GET /api/wallet/bonus
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "bonusBalance": 5000.00,
    "earnedThisMonth": 1500.00,
    "totalEarned": 25000.00,
    "sources": [
      {
        "source": "referral",
        "amount": 2000.00,
        "earnedAt": "2024-01-10T15:20:00Z"
      },
      {
        "source": "cashback",
        "amount": 1500.00,
        "earnedAt": "2024-01-12T09:45:00Z"
      }
    ]
  }
}
\`\`\`

#### 4.5 Get Crypto Wallets 🔐 ⏰
\`\`\`http
GET /api/wallet/crypto
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "cryptoWallets": [
      {
        "id": "wallet_id_1",
        "currency": "BTC",
        "balance": 0.00125000,
        "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "usdValue": 52.50,
        "nairaValue": 78750.00,
        "createdAt": "2024-01-01T00:00:00Z",
        "lastSyncAt": "2024-01-15T10:30:00Z"
      },
      {
        "id": "wallet_id_2",
        "currency": "ETH",
        "balance": 0.05000000,
        "address": "0x742d35Cc6634C0532925a3b8D4C0532925a3b8D4",
        "usdValue": 125.00,
        "nairaValue": 187500.00,
        "createdAt": "2024-01-01T00:00:00Z",
        "lastSyncAt": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
\`\`\`

#### 4.6 Create Crypto Wallet 🔐 ⏰
\`\`\`http
POST /api/wallet/crypto
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "USDT" // BTC, ETH, USDT, XRP, SOL, DOGE, ADA, TRX, BNB
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto wallet created successfully",
  "data": {
    "wallet": {
      "id": "wallet_id_3",
      "currency": "USDT",
      "balance": 0.00000000,
      "address": "TQn9Y2khEsLJW1ChVWFMSMeRDow5KcbLSE",
      "network": "TRC20",
      "usdValue": 0.00,
      "nairaValue": 0.00,
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 4.7 Get Specific Crypto Wallet 🔐 ⏰
\`\`\`http
GET /api/wallet/crypto/:walletId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "wallet": {
      "id": "wallet_id_1",
      "currency": "BTC",
      "balance": 0.00125000,
      "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "network": "Bitcoin",
      "usdValue": 52.50,
      "nairaValue": 78750.00,
      "createdAt": "2024-01-01T00:00:00Z",
      "lastSyncAt": "2024-01-15T10:30:00Z",
      "transactions": [
        {
          "id": "tx_id_1",
          "type": "deposit",
          "amount": 0.00125000,
          "txHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0",
          "status": "confirmed",
          "createdAt": "2024-01-10T15:20:00Z"
        }
      ]
    }
  }
}
\`\`\`

#### 4.8 Send Crypto 🔐 ⏰ 📱
\`\`\`http
POST /api/wallet/crypto/:walletId/send
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "toAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
  "amount": 0.001,
  "passphrase": "wallet_passphrase",
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto sent successfully",
  "data": {
    "transaction": {
      "id": "tx_id_2",
      "type": "withdrawal",
      "currency": "BTC",
      "amount": 0.001,
      "toAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "txHash": "b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1",
      "fee": 0.00001,
      "status": "pending",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 4.9 Sync Wallet Balance 🔐 ⏰
\`\`\`http
POST /api/wallet/crypto/:walletId/sync
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Wallet balance synced successfully",
  "data": {
    "wallet": {
      "id": "wallet_id_1",
      "currency": "BTC",
      "previousBalance": 0.00125000,
      "currentBalance": 0.00130000,
      "difference": 0.00005000,
      "lastSyncAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 4.10 Get Wallet Transactions 🔐 ⏰
\`\`\`http
GET /api/wallet/crypto/:walletId/transactions
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=deposit&status=confirmed
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": "tx_id_1",
        "type": "deposit",
        "currency": "BTC",
        "amount": 0.00125000,
        "txHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0",
        "fromAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "toAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "fee": 0.00001,
        "confirmations": 6,
        "status": "confirmed",
        "createdAt": "2024-01-10T15:20:00Z",
        "confirmedAt": "2024-01-10T16:30:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 87,
      "itemsPerPage": 20
    }
  }
}
\`\`\`

#### 4.11 Generate New Address 🔐 ⏰
\`\`\`http
POST /api/wallet/crypto/:walletId/address
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "New address generated successfully",
  "data": {
    "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
    "currency": "BTC",
    "network": "Bitcoin",
    "generatedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 4.12 Get Supported Coins 🔐 ⏰
\`\`\`http
GET /api/wallet/crypto/supported-coins
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "supportedCoins": [
      {
        "symbol": "BTC",
        "name": "Bitcoin",
        "network": "Bitcoin",
        "minDeposit": 0.00001,
        "minWithdrawal": 0.001,
        "withdrawalFee": 0.0005,
        "confirmationsRequired": 3,
        "isActive": true
      },
      {
        "symbol": "ETH",
        "name": "Ethereum",
        "network": "Ethereum",
        "minDeposit": 0.001,
        "minWithdrawal": 0.01,
        "withdrawalFee": 0.005,
        "confirmationsRequired": 12,
        "isActive": true
      },
      {
        "symbol": "USDT",
        "name": "Tether",
        "network": "TRC20",
        "minDeposit": 1,
        "minWithdrawal": 10,
        "withdrawalFee": 1,
        "confirmationsRequired": 19,
        "isActive": true
      }
    ]
  }
}
\`\`\`

#### 4.13 Get Crypto Transaction Status 🔐 ⏰
\`\`\`http
GET /api/wallet/crypto/transaction/:txHash/:coin
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transaction": {
      "txHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0",
      "currency": "BTC",
      "amount": 0.00125000,
      "fromAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "toAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "fee": 0.00001,
      "confirmations": 6,
      "requiredConfirmations": 3,
      "status": "confirmed",
      "blockHeight": 825000,
      "timestamp": "2024-01-10T15:20:00Z"
    }
  }
}
\`\`\`

#### 4.14 Estimate Transaction Fee 🔐 ⏰
\`\`\`http
GET /api/wallet/crypto/estimate-fee
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?currency=BTC&amount=0.001&priority=medium
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "currency": "BTC",
    "amount": 0.001,
    "fees": {
      "slow": {
        "fee": 0.00001,
        "estimatedTime": "60-120 minutes"
      },
      "medium": {
        "fee": 0.00005,
        "estimatedTime": "10-30 minutes"
      },
      "fast": {
        "fee": 0.0001,
        "estimatedTime": "5-10 minutes"
      }
    },
    "recommended": "medium"
  }
}
\`\`\`

#### 4.15 Transfer Funds 🔐 ⏰ 📱
\`\`\`http
POST /api/wallet/transfer
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "receiverEmail": "receiver@example.com",
  "amount": 50000,
  "description": "Payment for services",
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Transfer initiated successfully",
  "data": {
    "transfer": {
      "id": "transfer_id",
      "senderId": "user_id",
      "receiverId": "receiver_user_id",
      "amount": 50000,
      "fee": 100,
      "totalAmount": 50100,
      "description": "Payment for services",
      "status": "processing",
      "reference": "TXN_REF_123456",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 4.16 Internal Transfer 🔐 ⏰ 📱
\`\`\`http
POST /api/wallet/internal-transfer
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "receiverEmail": "receiver@example.com",
  "amount": 25000,
  "description": "Internal transfer",
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Internal transfer completed successfully",
  "data": {
    "transfer": {
      "id": "internal_transfer_id",
      "senderId": "user_id",
      "receiverId": "receiver_user_id",
      "amount": 25000,
      "fee": 0,
      "totalAmount": 25000,
      "description": "Internal transfer",
      "status": "completed",
      "reference": "INT_TXN_REF_123456",
      "createdAt": "2024-01-15T10:30:00Z",
      "completedAt": "2024-01-15T10:30:05Z"
    }
  }
}
\`\`\`

#### 4.17 Crypto Swap 🔐 ⏰ 📱
\`\`\`http
POST /api/wallet/crypto/swap
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "fromCurrency": "BTC",
  "toCurrency": "ETH",
  "amount": 0.001,
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto swap initiated successfully",
  "data": {
    "swap": {
      "id": "swap_id",
      "fromCurrency": "BTC",
      "toCurrency": "ETH",
      "fromAmount": 0.001,
      "toAmount": 0.025,
      "exchangeRate": 25.0,
      "fee": 0.00001,
      "status": "processing",
      "reference": "SWAP_REF_123456",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 4.18 Get Transaction History 🔐 ⏰
\`\`\`http
GET /api/wallet/transactions
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=all&status=all&startDate=2024-01-01&endDate=2024-01-31
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": "tx_id_1",
        "type": "deposit",
        "category": "funding",
        "amount": 100000,
        "currency": "NGN",
        "status": "completed",
        "description": "Wallet funding via Paystack",
        "reference": "PAY_REF_123456",
        "createdAt": "2024-01-10T15:20:00Z",
        "completedAt": "2024-01-10T15:25:00Z"
      },
      {
        "id": "tx_id_2",
        "type": "withdrawal",
        "category": "transfer",
        "amount": 50000,
        "currency": "NGN",
        "status": "completed",
        "description": "Transfer to John Smith",
        "reference": "TXN_REF_123456",
        "createdAt": "2024-01-12T09:45:00Z",
        "completedAt": "2024-01-12T09:50:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 10,
      "totalItems": 200,
      "itemsPerPage": 20
    },
    "summary": {
      "totalInflow": 500000,
      "totalOutflow": 350000,
      "netFlow": 150000
    }
  }
}
\`\`\`

#### 4.19 Export Transaction History 🔐 ⏰
\`\`\`http
GET /api/wallet/transactions/export
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?format=pdf&startDate=2024-01-01&endDate=2024-01-31
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Transaction history export generated successfully",
  "data": {
    "downloadUrl": "https://example.com/exports/transactions_2024-01.pdf",
    "format": "pdf",
    "recordCount": 87,
    "generatedAt": "2024-01-15T10:30:00Z",
    "expiresAt": "2024-01-16T10:30:00Z"
  }
}
\`\`\`

#### 4.20 Fund Wallet 🔐 ⏰
\`\`\`http
POST /api/wallet/fund
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "amount": 100000,
  "method": "card", // "card" or "bank_transfer"
  "gateway": "paystack" // "paystack" or "flutterwave"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Payment initialization successful",
  "data": {
    "paymentReference": "PAY_REF_123456",
    "authorizationUrl": "https://checkout.paystack.com/v3/hosted/pay/abc123",
    "accessCode": "abc123def456",
    "amount": 100000,
    "gateway": "paystack",
    "expiresAt": "2024-01-15T11:30:00Z"
  }
}
\`\`\`

#### 4.21 Verify Payment 🔐 ⏰
\`\`\`http
POST /api/wallet/verify-payment
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "reference": "PAY_REF_123456",
  "gateway": "paystack"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Payment verified successfully",
  "data": {
    "payment": {
      "reference": "PAY_REF_123456",
      "amount": 100000,
      "status": "success",
      "gateway": "paystack",
      "gatewayReference": "paystack_ref_789",
      "paidAt": "2024-01-15T10:35:00Z",
      "channel": "card",
      "cardDetails": {
        "last4": "1234",
        "brand": "visa",
        "bank": "GTBank"
      }
    },
    "wallet": {
      "previousBalance": 50000,
      "currentBalance": 150000,
      "creditedAmount": 100000
    }
  }
}
\`\`\`

#### 4.22 Withdraw Funds 🔐 ⏰ 📱
\`\`\`http
POST /api/wallet/withdraw
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "amount": 50000,
  "bankDetails": {
    "accountNumber": "0123456789",
    "bankCode": "058"
  },
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Withdrawal initiated successfully",
  "data": {
    "withdrawal": {
      "id": "withdrawal_id",
      "amount": 50000,
      "fee": 100,
      "totalAmount": 50100,
      "bankDetails": {
        "accountNumber": "0123456789",
        "accountName": "John Doe",
        "bankName": "GTBank",
        "bankCode": "058"
      },
      "status": "processing",
      "reference": "WTH_REF_123456",
      "estimatedArrival": "2024-01-15T12:30:00Z",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 4.23 Get Supported Banks 🔐 ⏰
\`\`\`http
GET /api/wallet/banks
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "banks": [
      {
        "name": "Access Bank",
        "code": "044",
        "slug": "access-bank",
        "isActive": true
      },
      {
        "name": "GTBank",
        "code": "058",
        "slug": "gtbank",
        "isActive": true
      },
      {
        "name": "First Bank",
        "code": "011",
        "slug": "first-bank",
        "isActive": true
      }
    ]
  }
}
\`\`\`

#### 4.24 Resolve Bank Account 🔐 ⏰
\`\`\`http
POST /api/wallet/resolve-bank
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "accountNumber": "0123456789",
  "bankCode": "058"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Bank account resolved successfully",
  "data": {
    "accountNumber": "0123456789",
    "accountName": "JOHN DOE",
    "bankName": "GTBank",
    "bankCode": "058"
  }
}
\`\`\`

---

## 5. Crypto Operations

### Base Route: `/api/crypto`

#### 5.1 Get Crypto Rates 🔓
\`\`\`http
GET /api/crypto/rates
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "rates": [
      {
        "currency": "BTC",
        "name": "Bitcoin",
        "buyRate": 42000.00,
        "sellRate": 41500.00,
        "usdRate": 42250.00,
        "change24h": 2.5,
        "lastUpdated": "2024-01-15T10:30:00Z"
      },
      {
        "currency": "ETH",
        "name": "Ethereum",
        "buyRate": 2500.00,
        "sellRate": 2480.00,
        "usdRate": 2490.00,
        "change24h": -1.2,
        "lastUpdated": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
\`\`\`

#### 5.2 Get Supported Coins 🔓
\`\`\`http
GET /api/crypto/supported
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "supportedCoins": [
      {
        "symbol": "BTC",
        "name": "Bitcoin",
        "network": "Bitcoin",
        "minBuy": 0.00001,
        "maxBuy": 10.0,
        "minSell": 0.00001,
        "maxSell": 10.0,
        "buyFee": 0.5,
        "sellFee": 0.5,
        "isActive": true,
        "icon": "https://example.com/icons/btc.png"
      },
      {
        "symbol": "ETH",
        "name": "Ethereum",
        "network": "Ethereum",
        "minBuy": 0.001,
        "maxBuy": 100.0,
        "minSell": 0.001,
        "maxSell": 100.0,
        "buyFee": 0.5,
        "sellFee": 0.5,
        "isActive": true,
        "icon": "https://example.com/icons/eth.png"
      }
    ]
  }
}
\`\`\`

#### 5.3 Get Chart Data 🔓
\`\`\`http
GET /api/crypto/chart/:coin
\`\`\`

**Query Parameters:**
\`\`\`
?period=24h&interval=1h
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "coin": "BTC",
    "period": "24h",
    "interval": "1h",
    "data": [
      {
        "timestamp": "2024-01-15T00:00:00Z",
        "open": 41800.00,
        "high": 42100.00,
        "low": 41750.00,
        "close": 42000.00,
        "volume": 1250.5
      },
      {
        "timestamp": "2024-01-15T01:00:00Z",
        "open": 42000.00,
        "high": 42300.00,
        "low": 41900.00,
        "close": 42250.00,
        "volume": 980.3
      }
    ]
  }
}
\`\`\`

#### 5.4 List Crypto Wallets 🔐 ⏰
\`\`\`http
GET /api/crypto/wallets
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "wallets": [
      {
        "id": "wallet_id_1",
        "currency": "BTC",
        "balance": 0.00125000,
        "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "usdValue": 52.50,
        "nairaValue": 78750.00,
        "createdAt": "2024-01-01T00:00:00Z"
      }
    ]
  }
}
\`\`\`

#### 5.5 Get Wallet Summary 🔐 ⏰
\`\`\`http
GET /api/crypto/summary
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "summary": {
      "totalUsdValue": 177.50,
      "totalNairaValue": 266250.00,
      "totalWallets": 2,
      "topPerformer": {
        "currency": "ETH",
        "change24h": 5.2
      },
      "portfolioDistribution": [
        {
          "currency": "BTC",
          "percentage": 29.6,
          "value": 52.50
        },
        {
          "currency": "ETH",
          "percentage": 70.4,
          "value": 125.00
        }
      ]
    }
  }
}
\`\`\`

#### 5.6 Create Crypto Wallet 🔐 ⏰
\`\`\`http
POST /api/crypto/wallets/create
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "coin": "USDT" // BTC, ETH, USDT, XRP, SOL, DOGE, ADA, TRX, BNB
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto wallet created successfully",
  "data": {
    "wallet": {
      "id": "wallet_id_3",
      "currency": "USDT",
      "balance": 0.00000000,
      "address": "TQn9Y2khEsLJW1ChVWFMSMeRDow5KcbLSE",
      "network": "TRC20",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 5.7 Get Deposit Info 🔐 ⏰
\`\`\`http
GET /api/crypto/deposit/:coin
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "depositInfo": {
      "currency": "BTC",
      "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "network": "Bitcoin",
      "qrCode": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
      "minDeposit": 0.00001,
      "confirmationsRequired": 3,
      "estimatedArrival": "10-30 minutes",
      "warning": "Only send BTC to this address. Sending other coins may result in permanent loss."
    }
  }
}
\`\`\`

#### 5.8 Get Crypto History 🔐 ⏰
\`\`\`http
GET /api/crypto/history
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=all&coin=BTC&status=completed
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": "crypto_tx_1",
        "type": "buy",
        "currency": "BTC",
        "amount": 0.001,
        "nairaAmount": 63000,
        "rate": 63000000,
        "fee": 315,
        "status": "completed",
        "reference": "CRYPTO_BUY_123456",
        "createdAt": "2024-01-10T15:20:00Z",
        "completedAt": "2024-01-10T15:25:00Z"
      },
      {
        "id": "crypto_tx_2",
        "type": "sell",
        "currency": "ETH",
        "amount": 0.01,
        "nairaAmount": 37500,
        "rate": 3750000,
        "fee": 187.5,
        "status": "completed",
        "reference": "CRYPTO_SELL_123456",
        "createdAt": "2024-01-12T09:45:00Z",
        "completedAt": "2024-01-12T09:50:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 87,
      "itemsPerPage": 20
    }
  }
}
\`\`\`

#### 5.9 Buy Crypto 🔐 ⏰ 📋 📱
\`\`\`http
POST /api/crypto/buy
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "coin": "BTC",
  "amount": 0.001,
  "paymentMethod": "wallet", // "wallet" or "card"
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto purchase initiated successfully",
  "data": {
    "order": {
      "id": "crypto_order_id",
      "type": "buy",
      "currency": "BTC",
      "amount": 0.001,
      "nairaAmount": 63000,
      "rate": 63000000,
      "fee": 315,
      "totalAmount": 63315,
      "paymentMethod": "wallet",
      "status": "processing",
      "reference": "CRYPTO_BUY_123456",
      "estimatedCompletion": "2-5 minutes",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 5.10 Sell Crypto 🔐 ⏰ 📋 📱
\`\`\`http
POST /api/crypto/sell
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "coin": "BTC",
  "amount": 0.001,
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto sale initiated successfully",
  "data": {
    "order": {
      "id": "crypto_order_id",
      "type": "sell",
      "currency": "BTC",
      "amount": 0.001,
      "nairaAmount": 62500,
      "rate": 62500000,
      "fee": 312.5,
      "netAmount": 62187.5,
      "status": "processing",
      "reference": "CRYPTO_SELL_123456",
      "estimatedCompletion": "2-5 minutes",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 5.11 Swap Crypto 🔐 ⏰ 📋 📱
\`\`\`http
POST /api/crypto/swap
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "fromCoin": "BTC",
  "toCoin": "ETH",
  "amount": 0.001,
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto swap initiated successfully",
  "data": {
    "swap": {
      "id": "crypto_swap_id",
      "fromCurrency": "BTC",
      "toCurrency": "ETH",
      "fromAmount": 0.001,
      "toAmount": 0.025,
      "exchangeRate": 25.0,
      "fee": 0.00001,
      "status": "processing",
      "reference": "CRYPTO_SWAP_123456",
      "estimatedCompletion": "2-5 minutes",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 5.12 Withdraw Crypto 🔐 ⏰ 📋 📱
\`\`\`http
POST /api/crypto/withdraw
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "coin": "BTC",
  "amount": 0.001,
  "toAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
  "network": "Bitcoin",
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto withdrawal initiated successfully",
  "data": {
    "withdrawal": {
      "id": "crypto_withdrawal_id",
      "currency": "BTC",
      "amount": 0.001,
      "toAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "network": "Bitcoin",
      "fee": 0.0005,
      "netAmount": 0.0005,
      "status": "processing",
      "reference": "CRYPTO_WTH_123456",
      "estimatedCompletion": "10-30 minutes",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 5.13 Sync Wallet Balance 🔐 ⏰
\`\`\`http
POST /api/crypto/sync/:coin
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Wallet balance synced successfully",
  "data": {
    "currency": "BTC",
    "previousBalance": 0.00125000,
    "currentBalance": 0.00130000,
    "difference": 0.00005000,
    "lastSyncAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 5.14 Sync All Wallets 🔐 ⏰
\`\`\`http
POST /api/crypto/sync-all
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "All wallets synced successfully",
  "data": {
    "syncedWallets": [
      {
        "currency": "BTC",
        "previousBalance": 0.00125000,
        "currentBalance": 0.00130000,
        "difference": 0.00005000
      },
      {
        "currency": "ETH",
        "previousBalance": 0.05000000,
        "currentBalance": 0.05000000,
        "difference": 0.00000000
      }
    ],
    "lastSyncAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 5.15 Get Transaction Status 🔐 ⏰
\`\`\`http
GET /api/crypto/transaction/:transactionId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transaction": {
      "id": "crypto_tx_1",
      "type": "buy",
      "currency": "BTC",
      "amount": 0.001,
      "nairaAmount": 63000,
      "rate": 63000000,
      "fee": 315,
      "status": "completed",
      "reference": "CRYPTO_BUY_123456",
      "txHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0",
      "confirmations": 6,
      "createdAt": "2024-01-10T15:20:00Z",
      "completedAt": "2024-01-10T15:25:00Z"
    }
  }
}
\`\`\`

#### 5.16 Estimate Transaction Fee 🔐 ⏰
\`\`\`http
GET /api/crypto/fee/:coin
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?amount=0.001&priority=medium
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "currency": "BTC",
    "amount": 0.001,
    "fees": {
      "slow": {
        "fee": 0.00001,
        "estimatedTime": "60-120 minutes"
      },
      "medium": {
        "fee": 0.00005,
        "estimatedTime": "10-30 minutes"
      },
      "fast": {
        "fee": 0.0001,
        "estimatedTime": "5-10 minutes"
      }
    },
    "recommended": "medium"
  }
}
\`\`\`

#### 5.17 Validate Address 🔐 ⏰
\`\`\`http
POST /api/crypto/validate-address
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
  "currency": "BTC"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "isValid": true,
    "currency": "BTC",
    "network": "Bitcoin",
    "addressType": "bech32",
    "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh"
  }
}
\`\`\`

#### 5.18 Process Deposit Webhook 🔓
\`\`\`http
POST /api/crypto/webhook/deposit
\`\`\`

**Request Body:**
\`\`\`json
{
  "txHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0",
  "currency": "BTC",
  "amount": 0.001,
  "toAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
  "confirmations": 3,
  "status": "confirmed"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Deposit webhook processed successfully"
}
\`\`\`

---

## 6. Crypto Orders

### Base Route: `/api/crypto-order`

#### 6.1 Get Crypto Prices 🔓
\`\`\`http
GET /api/crypto-order/prices
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "prices": [
      {
        "currency": "BTC",
        "name": "Bitcoin",
        "buyPrice": 42000.00,
        "sellPrice": 41500.00,
        "usdPrice": 42250.00,
        "change24h": 2.5,
        "lastUpdated": "2024-01-15T10:30:00Z"
      },
      {
        "currency": "ETH",
        "name": "Ethereum",
        "buyPrice": 2500.00,
        "sellPrice": 2480.00,
        "usdPrice": 2490.00,
        "change24h": -1.2,
        "lastUpdated": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
\`\`\`

#### 6.2 Create Buy Order 🔐
\`\`\`http
POST /api/crypto-order/buy
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "BTC", // BTC, ETH, XRP, BNB, SOL, DOGE, ADA, TRX
  "amount": 0.001,
  "userWalletAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Buy order created successfully",
  "data": {
    "order": {
      "id": "order_id",
      "type": "buy",
      "currency": "BTC",
      "amount": 0.001,
      "nairaAmount": 63000,
      "rate": 63000000,
      "fee": 315,
      "totalAmount": 63315,
      "userWalletAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "adminWalletAddress": "bc1qadmin2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "status": "pending_payment",
      "orderNumber": "ORD_BUY_123456",
      "paymentDeadline": "2024-01-15T11:30:00Z",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 6.3 Create Sell Order 🔐
\`\`\`http
POST /api/crypto-order/sell
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "BTC", // BTC, ETH, XRP, BNB, SOL, DOGE, ADA, TRX
  "amount": 0.001
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Sell order created successfully",
  "data": {
    "order": {
      "id": "order_id",
      "type": "sell",
      "currency": "BTC",
      "amount": 0.001,
      "nairaAmount": 62500,
      "rate": 62500000,
      "fee": 312.5,
      "netAmount": 62187.5,
      "adminWalletAddress": "bc1qadmin2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "status": "pending_crypto_transfer",
      "orderNumber": "ORD_SELL_123456",
      "transferDeadline": "2024-01-15T11:30:00Z",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 6.4 Get User Crypto Orders 🔐
\`\`\`http
GET /api/crypto-order/user
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=buy&status=completed&currency=BTC
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "orders": [
      {
        "id": "order_id_1",
        "type": "buy",
        "currency": "BTC",
        "amount": 0.001,
        "nairaAmount": 63000,
        "rate": 63000000,
        "fee": 315,
        "totalAmount": 63315,
        "status": "completed",
        "orderNumber": "ORD_BUY_123456",
        "createdAt": "2024-01-10T15:20:00Z",
        "completedAt": "2024-01-10T15:45:00Z"
      },
      {
        "id": "order_id_2",
        "type": "sell",
        "currency": "ETH",
        "amount": 0.01,
        "nairaAmount": 37500,
        "rate": 3750000,
        "fee": 187.5,
        "netAmount": 37312.5,
        "status": "completed",
        "orderNumber": "ORD_SELL_123456",
        "createdAt": "2024-01-12T09:45:00Z",
        "completedAt": "2024-01-12T10:15:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 87,
      "itemsPerPage": 20
    }
  }
}
\`\`\`

#### 6.5 Get Crypto Order by ID 🔐
\`\`\`http
GET /api/crypto-order/:orderId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "order": {
      "id": "order_id",
      "type": "buy",
      "currency": "BTC",
      "amount": 0.001,
      "nairaAmount": 63000,
      "rate": 63000000,
      "fee": 315,
      "totalAmount": 63315,
      "userWalletAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "adminWalletAddress": "bc1qadmin2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
      "status": "pending_approval",
      "orderNumber": "ORD_BUY_123456",
      "paymentReference": "PAY_REF_789",
      "transactionHash": null,
      "rejectionReason": null,
      "createdAt": "2024-01-15T10:30:00Z",
      "updatedAt": "2024-01-15T10:35:00Z",
      "timeline": [
        {
          "status": "pending_payment",
          "timestamp": "2024-01-15T10:30:00Z",
          "description": "Order created, awaiting payment"
        },
        {
          "status": "payment_confirmed",
          "timestamp": "2024-01-15T10:35:00Z",
          "description": "Payment confirmed, awaiting admin approval"
        }
      ]
    }
  }
}
\`\`\`

#### 6.6 Cancel Crypto Order 🔐
\`\`\`http
PATCH /api/crypto-order/:orderId/cancel
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Order cancelled successfully",
  "data": {
    "order": {
      "id": "order_id",
      "status": "cancelled",
      "cancelledAt": "2024-01-15T10:45:00Z",
      "refundAmount": 63315,
      "refundStatus": "processing"
    }
  }
}
\`\`\`

#### 6.7 Get All Crypto Orders (Admin) 👑
\`\`\`http
GET /api/crypto-order/admin/orders
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=buy&status=pending_approval&currency=BTC&userId=user_id
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "orders": [
      {
        "id": "order_id_1",
        "type": "buy",
        "currency": "BTC",
        "amount": 0.001,
        "nairaAmount": 63000,
        "user": {
          "id": "user_id",
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@example.com"
        },
        "status": "pending_approval",
        "orderNumber": "ORD_BUY_123456",
        "createdAt": "2024-01-15T10:30:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 10,
      "totalItems": 200,
      "itemsPerPage": 20
    },
    "summary": {
      "totalPending": 15,
      "totalApproved": 150,
      "totalRejected": 35,
      "totalVolume": 5000000
    }
  }
}
\`\`\`

#### 6.8 Approve Crypto Order (Admin) 👑
\`\`\`http
PATCH /api/crypto-order/admin/orders/:orderId/approve
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Order approved successfully",
  "data": {
    "order": {
      "id": "order_id",
      "status": "approved",
      "approvedBy": "admin_id",
      "approvedAt": "2024-01-15T10:45:00Z"
    }
  }
}
\`\`\`

#### 6.9 Reject Crypto Order (Admin) 👑
\`\`\`http
PATCH /api/crypto-order/admin/orders/:orderId/reject
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "rejectionReason": "Invalid wallet address provided"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Order rejected successfully",
  "data": {
    "order": {
      "id": "order_id",
      "status": "rejected",
      "rejectionReason": "Invalid wallet address provided",
      "rejectedBy": "admin_id",
      "rejectedAt": "2024-01-15T10:45:00Z",
      "refundAmount": 63315,
      "refundStatus": "processing"
    }
  }
}
\`\`\`

#### 6.10 Process Crypto Transaction (Admin) 👑
\`\`\`http
PATCH /api/crypto-order/admin/orders/:orderId/process
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "transactionHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Transaction processed successfully",
  "data": {
    "order": {
      "id": "order_id",
      "status": "completed",
      "transactionHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0",
      "processedBy": "admin_id",
      "processedAt": "2024-01-15T10:45:00Z",
      "completedAt": "2024-01-15T10:45:00Z"
    }
  }
}
\`\`\`

#### 6.11 Get Admin Wallet Addresses (Admin) 👑
\`\`\`http
GET /api/crypto-order/admin/wallets
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "wallets": [
      {
        "id": "admin_wallet_id_1",
        "currency": "BTC",
        "address": "bc1qadmin2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "network": "Bitcoin",
        "balance": 5.25000000,
        "isActive": true,
        "createdAt": "2024-01-01T00:00:00Z"
      },
      {
        "id": "admin_wallet_id_2",
        "currency": "ETH",
        "address": "0x742d35Cc6634C0532925a3b8D4C0532925a3b8D4",
        "network": "Ethereum",
        "balance": 125.50000000,
        "isActive": true,
        "createdAt": "2024-01-01T00:00:00Z"
      }
    ]
  }
}
\`\`\`

#### 6.12 Create Admin Wallet Address (Admin) 👑
\`\`\`http
POST /api/crypto-order/admin/wallets
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "USDT", // BTC, ETH, XRP, BNB, SOL, DOGE, ADA, TRX
  "address": "TQn9Y2khEsLJW1ChVWFMSMeRDow5KcbLSE",
  "network": "TRC20"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Admin wallet address created successfully",
  "data": {
    "wallet": {
      "id": "admin_wallet_id_3",
      "currency": "USDT",
      "address": "TQn9Y2khEsLJW1ChVWFMSMeRDow5KcbLSE",
      "network": "TRC20",
      "balance": 0.00000000,
      "isActive": true,
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 6.13 Update Admin Wallet Address (Admin) 👑
\`\`\`http
PATCH /api/crypto-order/admin/wallets/:walletId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "USDT",
  "address": "TQn9Y2khEsLJW1ChVWFMSMeRDow5KcbLSE",
  "network": "TRC20"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Admin wallet address updated successfully",
  "data": {
    "wallet": {
      "id": "admin_wallet_id_3",
      "currency": "USDT",
      "address": "TQn9Y2khEsLJW1ChVWFMSMeRDow5KcbLSE",
      "network": "TRC20",
      "isActive": true,
      "updatedAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

---

## 7. Crypto Rates

### Base Route: `/api/crypto-rate`

#### 7.1 Get All Crypto Rates 🔓
\`\`\`http
GET /api/crypto-rate
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "rates": [
      {
        "currency": "BTC",
        "name": "Bitcoin",
        "buyRate": 42000.00,
        "sellRate": 41500.00,
        "usdRate": 42250.00,
        "change24h": 2.5,
        "isActive": true,
        "lastUpdated": "2024-01-15T10:30:00Z",
        "metadata": {
          "notes": "Updated based on market conditions",
          "source": "Binance API"
        }
      },
      {
        "currency": "ETH",
        "name": "Ethereum",
        "buyRate": 2500.00,
        "sellRate": 2480.00,
        "usdRate": 2490.00,
        "change24h": -1.2,
        "isActive": true,
        "lastUpdated": "2024-01-15T10:30:00Z",
        "metadata": {
          "notes": "Stable trading range",
          "source": "Binance API"
        }
      }
    ]
  }
}
\`\`\`

#### 7.2 Get Rate by Currency 🔓
\`\`\`http
GET /api/crypto-rate/:currency
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "rate": {
      "currency": "BTC",
      "name": "Bitcoin",
      "buyRate": 42000.00,
      "sellRate": 41500.00,
      "usdRate": 42250.00,
      "change24h": 2.5,
      "change7d": 8.3,
      "change30d": -5.2,
      "marketCap": 825000000000,
      "volume24h": 15000000000,
      "isActive": true,
      "lastUpdated": "2024-01-15T10:30:00Z",
      "priceHistory": [
        {
          "timestamp": "2024-01-15T09:30:00Z",
          "price": 41800.00
        },
        {
          "timestamp": "2024-01-15T10:00:00Z",
          "price": 42100.00
        },
        {
          "timestamp": "2024-01-15T10:30:00Z",
          "price": 42250.00
        }
      ],
      "metadata": {
        "notes": "Updated based on market conditions",
        "source": "Binance API",
        "spread": 1.2
      }
    }
  }
}
\`\`\`

#### 7.3 Create Crypto Rate (Admin) 👑
\`\`\`http
POST /api/crypto-rate
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "SOL",
  "buyRate": 95.50,
  "sellRate": 94.80,
  "metadata": {
    "notes": "New listing for Solana",
    "source": "Manual entry"
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto rate created successfully",
  "data": {
    "rate": {
      "currency": "SOL",
      "name": "Solana",
      "buyRate": 95.50,
      "sellRate": 94.80,
      "usdRate": 95.15,
      "change24h": 0.0,
      "isActive": true,
      "createdAt": "2024-01-15T10:30:00Z",
      "createdBy": "admin_id",
      "metadata": {
        "notes": "New listing for Solana",
        "source": "Manual entry"
      }
    }
  }
}
\`\`\`

#### 7.4 Update Crypto Rate (Admin) 👑
\`\`\`http
PUT /api/crypto-rate/:currency
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "buyRate": 42500.00,
  "sellRate": 42000.00,
  "metadata": {
    "notes": "Updated due to market volatility",
    "source": "Binance API"
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto rate updated successfully",
  "data": {
    "rate": {
      "currency": "BTC",
      "name": "Bitcoin",
      "buyRate": 42500.00,
      "sellRate": 42000.00,
      "usdRate": 42250.00,
      "change24h": 3.2,
      "isActive": true,
      "lastUpdated": "2024-01-15T10:45:00Z",
      "updatedBy": "admin_id",
      "metadata": {
        "notes": "Updated due to market volatility",
        "source": "Binance API"
      }
    }
  }
}
\`\`\`

#### 7.5 Toggle Rate Status (Admin) 👑
\`\`\`http
PATCH /api/crypto-rate/:currency/toggle
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Rate status toggled successfully",
  "data": {
    "rate": {
      "currency": "BTC",
      "isActive": false,
      "updatedAt": "2024-01-15T10:45:00Z",
      "updatedBy": "admin_id"
    }
  }
}
\`\`\`

#### 7.6 Delete Crypto Rate (Admin) 👑
\`\`\`http
DELETE /api/crypto-rate/:currency
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Crypto rate deleted successfully"
}
\`\`\`

#### 7.7 Bulk Update Rates (Admin) 👑
\`\`\`http
POST /api/crypto-rate/bulk-update
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "rates": [
    {
      "currency": "BTC",
      "buyRate": 42500.00,
      "sellRate": 42000.00
    },
    {
      "currency": "ETH",
      "buyRate": 2520.00,
      "sellRate": 2500.00
    },
    {
      "currency": "USDT",
      "buyRate": 1500.00,
      "sellRate": 1480.00
    }
  ]
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Bulk rate update completed successfully",
  "data": {
    "updatedRates": [
      {
        "currency": "BTC",
        "buyRate": 42500.00,
        "sellRate": 42000.00,
        "status": "updated"
      },
      {
        "currency": "ETH",
        "buyRate": 2520.00,
        "sellRate": 2500.00,
        "status": "updated"
      },
      {
        "currency": "USDT",
        "buyRate": 1500.00,
        "sellRate": 1480.00,
        "status": "updated"
      }
    ],
    "updatedAt": "2024-01-15T10:45:00Z",
    "updatedBy": "admin_id"
  }
}
\`\`\`

---

## 8. Bill Payments

### Base Route: `/api/bill`

#### 8.1 Get Providers 🔓
\`\`\`http
GET /api/bill/providers
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "providers": {
      "airtime": [
        {
          "code": "MTN",
          "name": "MTN Nigeria",
          "logo": "https://example.com/logos/mtn.png",
          "isActive": true
        },
        {
          "code": "GLO",
          "name": "Globacom",
          "logo": "https://example.com/logos/glo.png",
          "isActive": true
        },
        {
          "code": "AIRTEL",
          "name": "Airtel Nigeria",
          "logo": "https://example.com/logos/airtel.png",
          "isActive": true
        },
        {
          "code": "9MOBILE",
          "name": "9mobile",
          "logo": "https://example.com/logos/9mobile.png",
          "isActive": true
        }
      ],
      "electricity": [
        {
          "code": "EKEDC",
          "name": "Eko Electricity Distribution Company",
          "logo": "https://example.com/logos/ekedc.png",
          "isActive": true
        },
        {
          "code": "IKEDC",
          "name": "Ikeja Electric",
          "logo": "https://example.com/logos/ikedc.png",
          "isActive": true
        }
      ],
      "tv": [
        {
          "code": "DSTV",
          "name": "DStv",
          "logo": "https://example.com/logos/dstv.png",
          "isActive": true
        },
        {
          "code": "GOTV",
          "name": "GOtv",
          "logo": "https://example.com/logos/gotv.png",
          "isActive": true
        }
      ],
      "betting": [
        {
          "code": "BET9JA",
          "name": "Bet9ja",
          "logo": "https://example.com/logos/bet9ja.png",
          "isActive": true
        },
        {
          "code": "SPORTYBET",
          "name": "SportyBet",
          "logo": "https://example.com/logos/sportybet.png",
          "isActive": true
        }
      ]
    }
  }
}
\`\`\`

#### 8.2 Get Data Plans 🔓
\`\`\`http
GET /api/bill/data-plans/:network
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "network": "MTN",
    "dataPlans": [
      {
        "code": "MTN_1GB_30DAYS",
        "name": "1GB - 30 Days",
        "amount": 350,
        "validity": "30 days",
        "description": "1GB data valid for 30 days"
      },
      {
        "code": "MTN_2GB_30DAYS",
        "name": "2GB - 30 Days",
        "amount": 700,
        "validity": "30 days",
        "description": "2GB data valid for 30 days"
      },
      {
        "code": "MTN_5GB_30DAYS",
        "name": "5GB - 30 Days",
        "amount": 1500,
        "validity": "30 days",
        "description": "5GB data valid for 30 days"
      }
    ]
  }
}
\`\`\`

#### 8.3 Get TV Packages 🔓
\`\`\`http
GET /api/bill/tv-packages/:provider
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "provider": "DSTV",
    "packages": [
      {
        "code": "DSTV_COMPACT",
        "name": "DStv Compact",
        "amount": 9000,
        "validity": "30 days",
        "description": "Over 100 channels including sports and movies"
      },
      {
        "code": "DSTV_COMPACT_PLUS",
        "name": "DStv Compact Plus",
        "amount": 14250,
        "validity": "30 days",
        "description": "Over 140 channels with premium content"
      },
      {
        "code": "DSTV_PREMIUM",
        "name": "DStv Premium",
        "amount": 21000,
        "validity": "30 days",
        "description": "All channels including exclusive premium content"
      }
    ]
  }
}
\`\`\`

#### 8.4 Get Internet Plans 🔓
\`\`\`http
GET /api/bill/internet-plans/:provider
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "provider": "SMILE",
    "internetPlans": [
      {
        "code": "SMILE_10GB",
        "name": "10GB Monthly",
        "amount": 3500,
        "validity": "30 days",
        "description": "10GB internet data for 30 days"
      },
      {
        "code": "SMILE_25GB",
        "name": "25GB Monthly",
        "amount": 8000,
        "validity": "30 days",
        "description": "25GB internet data for 30 days"
      }
    ]
  }
}
\`\`\`

#### 8.5 Buy Airtime 🔐 ⏰ 📱
\`\`\`http
POST /api/bill/airtime
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "network": "MTN", // MTN, GLO, AIRTEL, 9MOBILE
  "phoneNumber": "08012345678",
  "amount": 1000,
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Airtime purchase successful",
  "data": {
    "bill": {
      "id": "bill_id",
      "type": "airtime",
      "network": "MTN",
      "phoneNumber": "08012345678",
      "amount": 1000,
      "fee": 10,
      "totalAmount": 1010,
      "status": "successful",
      "reference": "AIRTIME_REF_123456",
      "providerReference": "MTN_REF_789",
      "createdAt": "2024-01-15T10:30:00Z",
      "completedAt": "2024-01-15T10:30:15Z"
    }
  }
}
\`\`\`

#### 8.6 Buy Data 🔐 ⏰ 📱
\`\`\`http
POST /api/bill/data
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "network": "MTN",
  "phoneNumber": "08012345678",
  "planCode": "MTN_1GB_30DAYS",
  "amount": 350,
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Data purchase successful",
  "data": {
    "bill": {
      "id": "bill_id",
      "type": "data",
      "network": "MTN",
      "phoneNumber": "08012345678",
      "planCode": "MTN_1GB_30DAYS",
      "planName": "1GB - 30 Days",
      "amount": 350,
      "fee": 5,
      "totalAmount": 355,
      "status": "successful",
      "reference": "DATA_REF_123456",
      "providerReference": "MTN_DATA_REF_789",
      "createdAt": "2024-01-15T10:30:00Z",
      "completedAt": "2024-01-15T10:30:20Z"
    }
  }
}
\`\`\`

#### 8.7 Pay Electricity 🔐 ⏰ 📱
\`\`\`http
POST /api/bill/electricity
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "provider": "EKEDC",
  "meterNumber": "12345678901",
  "meterType": "prepaid", // "prepaid" or "postpaid"
  "amount": 5000,
  "customerName": "John Doe",
  "customerAddress": "123 Main Street, Lagos",
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Electricity payment successful",
  "data": {
    "bill": {
      "id": "bill_id",
      "type": "electricity",
      "provider": "EKEDC",
      "meterNumber": "12345678901",
      "meterType": "prepaid",
      "customerName": "John Doe",
      "amount": 5000,
      "fee": 50,
      "totalAmount": 5050,
      "units": "45.5 kWh",
      "token": "1234-5678-9012-3456",
      "status": "successful",
      "reference": "ELEC_REF_123456",
      "providerReference": "EKEDC_REF_789",
      "createdAt": "2024-01-15T10:30:00Z",
      "completedAt": "2024-01-15T10:30:25Z"
    }
  }
}
\`\`\`

#### 8.8 Pay TV Subscription 🔐 ⏰ 📱
\`\`\`http
POST /api/bill/tv
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "provider": "DSTV",
  "smartcardNumber": "1234567890",
  "packageCode": "DSTV_COMPACT",
  "amount": 9000,
  "customerName": "John Doe",
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "TV subscription payment successful",
  "data": {
    "bill": {
      "id": "bill_id",
      "type": "tv",
      "provider": "DSTV",
      "smartcardNumber": "1234567890",
      "packageCode": "DSTV_COMPACT",
      "packageName": "DStv Compact",
      "customerName": "John Doe",
      "amount": 9000,
      "fee": 90,
      "totalAmount": 9090,
      "validUntil": "2024-02-15T23:59:59Z",
      "status": "successful",
      "reference": "TV_REF_123456",
      "providerReference": "DSTV_REF_789",
      "createdAt": "2024-01-15T10:30:00Z",
      "completedAt": "2024-01-15T10:30:30Z"
    }
  }
}
\`\`\`

#### 8.9 Pay Internet 🔐 ⏰ 📱
\`\`\`http
POST /api/bill/internet
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "provider": "SMILE",
  "accountId": "1234567890",
  "planCode": "SMILE_10GB",
  "amount": 3500,
  "customerName": "John Doe",
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Internet payment successful",
  "data": {
    "bill": {
      "id": "bill_id",
      "type": "internet",
      "provider": "SMILE",
      "accountId": "1234567890",
      "planCode": "SMILE_10GB",
      "planName": "10GB Monthly",
      "customerName": "John Doe",
      "amount": 3500,
      "fee": 35,
      "totalAmount": 3535,
      "validUntil": "2024-02-15T23:59:59Z",
      "status": "successful",
      "reference": "INTERNET_REF_123456",
      "providerReference": "SMILE_REF_789",
      "createdAt": "2024-01-15T10:30:00Z",
      "completedAt": "2024-01-15T10:30:35Z"
    }
  }
}
\`\`\`

#### 8.10 Topup Betting 🔐 ⏰ 📱
\`\`\`http
POST /api/bill/betting
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "provider": "BET9JA",
  "customerId": "1234567890",
  "amount": 10000,
  "customerName": "John Doe",
  "transactionPin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Betting account topup successful",
  "data": {
    "bill": {
      "id": "bill_id",
      "type": "betting",
      "provider": "BET9JA",
      "customerId": "1234567890",
      "customerName": "John Doe",
      "amount": 10000,
      "fee": 100,
      "totalAmount": 10100,
      "status": "successful",
      "reference": "BETTING_REF_123456",
      "providerReference": "BET9JA_REF_789",
      "createdAt": "2024-01-15T10:30:00Z",
      "completedAt": "2024-01-15T10:30:40Z"
    }
  }
}
\`\`\`

#### 8.11 Validate Meter 🔐 ⏰
\`\`\`http
POST /api/bill/validate/meter
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "provider": "EKEDC",
  "meterNumber": "12345678901",
  "meterType": "prepaid"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Meter validation successful",
  "data": {
    "meterInfo": {
      "meterNumber": "12345678901",
      "meterType": "prepaid",
      "customerName": "JOHN DOE",
      "customerAddress": "123 MAIN STREET, LAGOS",
      "provider": "EKEDC",
      "isValid": true
    }
  }
}
\`\`\`

#### 8.12 Validate Smartcard 🔐 ⏰
\`\`\`http
POST /api/bill/validate/smartcard
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "provider": "DSTV",
  "smartcardNumber": "1234567890"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Smartcard validation successful",
  "data": {
    "smartcardInfo": {
      "smartcardNumber": "1234567890",
      "customerName": "JOHN DOE",
      "currentPackage": "DStv Compact",
      "expiryDate": "2024-01-20T23:59:59Z",
      "provider": "DSTV",
      "isValid": true,
      "balance": 0
    }
  }
}
\`\`\`

#### 8.13 Validate Internet Account 🔐 ⏰
\`\`\`http
POST /api/bill/validate/internet
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "provider": "SMILE",
  "accountId": "1234567890"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Internet account validation successful",
  "data": {
    "accountInfo": {
      "accountId": "1234567890",
      "customerName": "JOHN DOE",
      "currentPlan": "10GB Monthly",
      "expiryDate": "2024-01-20T23:59:59Z",
      "provider": "SMILE",
      "isValid": true,
      "balance": 2.5
    }
  }
}
\`\`\`

#### 8.14 Get Bill History 🔐 ⏰
\`\`\`http
GET /api/bill/history
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=airtime&status=successful&startDate=2024-01-01&endDate=2024-01-31
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "bills": [
      {
        "id": "bill_id_1",
        "type": "airtime",
        "network": "MTN",
        "phoneNumber": "08012345678",
        "amount": 1000,
        "fee": 10,
        "totalAmount": 1010,
        "status": "successful",
        "reference": "AIRTIME_REF_123456",
        "createdAt": "2024-01-10T15:20:00Z",
        "completedAt": "2024-01-10T15:20:15Z"
      },
      {
        "id": "bill_id_2",
        "type": "electricity",
        "provider": "EKEDC",
        "meterNumber": "12345678901",
        "amount": 5000,
        "fee": 50,
        "totalAmount": 5050,
        "units": "45.5 kWh",
        "token": "1234-5678-9012-3456",
        "status": "successful",
        "reference": "ELEC_REF_123456",
        "createdAt": "2024-01-12T09:45:00Z",
        "completedAt": "2024-01-12T09:45:25Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 10,
      "totalItems": 200,
      "itemsPerPage": 20
    },
    "summary": {
      "totalSpent": 150000,
      "totalTransactions": 200,
      "mostUsedService": "airtime"
    }
  }
}
\`\`\`

#### 8.15 Export Bill History 🔐 ⏰
\`\`\`http
GET /api/bill/history/export
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?format=pdf&startDate=2024-01-01&endDate=2024-01-31&type=all
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Bill history export generated successfully",
  "data": {
    "downloadUrl": "https://example.com/exports/bills_2024-01.pdf",
    "format": "pdf",
    "recordCount": 87,
    "generatedAt": "2024-01-15T10:30:00Z",
    "expiresAt": "2024-01-16T10:30:00Z"
  }
}
\`\`\`

#### 8.16 Get Bill Receipt 🔐 ⏰
\`\`\`http
GET /api/bill/receipt/:billId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "receipt": {
      "billId": "bill_id",
      "type": "electricity",
      "provider": "EKEDC",
      "meterNumber": "12345678901",
      "customerName": "John Doe",
      "amount": 5000,
      "fee": 50,
      "totalAmount": 5050,
      "units": "45.5 kWh",
      "token": "1234-5678-9012-3456",
      "status": "successful",
      "reference": "ELEC_REF_123456",
      "providerReference": "EKEDC_REF_789",
      "paidAt": "2024-01-15T10:30:25Z",
      "receiptUrl": "https://example.com/receipts/bill_id.pdf"
    }
  }
}
\`\`\`

#### 8.17 Query Bill Status 🔐 ⏰
\`\`\`http
GET /api/bill/status/:billId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "bill": {
      "id": "bill_id",
      "status": "successful",
      "reference": "ELEC_REF_123456",
      "providerReference": "EKEDC_REF_789",
      "providerStatus": "COMPLETED",
      "lastChecked": "2024-01-15T10:30:00Z",
      "timeline": [
        {
          "status": "pending",
          "timestamp": "2024-01-15T10:30:00Z",
          "description": "Payment initiated"
        },
        {
          "status": "processing",
          "timestamp": "2024-01-15T10:30:10Z",
          "description": "Payment being processed"
        },
        {
          "status": "successful",
          "timestamp": "2024-01-15T10:30:25Z",
          "description": "Payment completed successfully"
        }
      ]
    }
  }
}
\`\`\`

#### 8.18 Dispute Bill 🔐 ⏰
\`\`\`http
POST /api/bill/dispute
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "billId": "bill_id",
  "reason": "Service not delivered",
  "description": "I paid for electricity but did not receive the token",
  "evidence": ["receipt_url", "screenshot_url"]
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Dispute submitted successfully",
  "data": {
    "dispute": {
      "id": "dispute_id",
      "billId": "bill_id",
      "reason": "Service not delivered",
      "description": "I paid for electricity but did not receive the token",
      "status": "pending_review",
      "reference": "DISPUTE_REF_123456",
      "submittedAt": "2024-01-15T10:30:00Z",
      "estimatedResolution": "2024-01-17T10:30:00Z"
    }
  }
}
\`\`\`

---

## 9. Gift Cards

### Base Route: `/api/giftcard`

#### 9.1 Get Available Rates 🔓
\`\`\`http
GET /api/giftcard/rates
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCardRates": [
      {
        "cardType": "physical",
        "brand": "Amazon",
        "cardName": "Amazon Gift Card",
        "currency": "USD",
        "minAmount": 25,
        "maxAmount": 500,
        "cardImage": "https://example.com/cards/amazon.png",
        "rateRanges": [
          {
            "minAmount": 25,
            "maxAmount": 100,
            "ratePerUnit": 0.82
          },
          {
            "minAmount": 101,
            "maxAmount": 500,
            "ratePerUnit": 0.85
          }
        ],
        "isActive": true,
        "lastUpdated": "2024-01-15T10:30:00Z"
      },
      {
        "cardType": "e-code",
        "brand": "iTunes",
        "cardName": "iTunes Gift Card",
        "currency": "USD",
        "minAmount": 10,
        "maxAmount": 200,
        "cardImage": "https://example.com/cards/itunes.png",
        "rateRanges": [
          {
            "minAmount": 10,
            "maxAmount": 50,
            "ratePerUnit": 0.78
          },
          {
            "minAmount": 51,
            "maxAmount": 200,
            "ratePerUnit": 0.80
          }
        ],
        "isActive": true,
        "lastUpdated": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
\`\`\`

#### 9.2 Sell Gift Card 🔐 ⏰ 📱
\`\`\`http
POST /api/giftcard/sell
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
Content-Type: multipart/form-data
\`\`\`

**Request Body (Form Data):**
\`\`\`
cardType: physical
brand: Amazon
amount: 100
currency: USD
cardCode: ABCD-EFGH-IJKL-MNOP
cardPin: 1234
expiryDate: 2025-12-31
cardImage: [file upload]
transactionPin: 123456
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card submitted for review",
  "data": {
    "giftCard": {
      "id": "giftcard_id",
      "cardType": "physical",
      "brand": "Amazon",
      "amount": 100,
      "currency": "USD",
      "estimatedValue": 82000,
      "rate": 0.82,
      "cardCode": "ABCD-****-****-MNOP",
      "status": "pending_validation",
      "reference": "GC_REF_123456",
      "submittedAt": "2024-01-15T10:30:00Z",
      "estimatedProcessingTime": "2-24 hours"
    }
  }
}
\`\`\`

#### 9.3 Get My Gift Cards 🔐 ⏰
\`\`\`http
GET /api/giftcard/my-cards
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&status=completed&brand=Amazon
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCards": [
      {
        "id": "giftcard_id_1",
        "cardType": "physical",
        "brand": "Amazon",
        "amount": 100,
        "currency": "USD",
        "estimatedValue": 82000,
        "actualValue": 82000,
        "rate": 0.82,
        "status": "completed",
        "reference": "GC_REF_123456",
        "submittedAt": "2024-01-10T15:20:00Z",
        "completedAt": "2024-01-10T18:45:00Z"
      },
      {
        "id": "giftcard_id_2",
        "cardType": "e-code",
        "brand": "iTunes",
        "amount": 50,
        "currency": "USD",
        "estimatedValue": 39000,
        "actualValue": 39000,
        "rate": 0.78,
        "status": "completed",
        "reference": "GC_REF_123457",
        "submittedAt": "2024-01-12T09:45:00Z",
        "completedAt": "2024-01-12T11:30:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 87,
      "itemsPerPage": 20
    },
    "summary": {
      "totalEarned": 500000,
      "totalCards": 87,
      "averageRate": 0.81
    }
  }
}
\`\`\`

#### 9.4 Get Gift Card Details 🔐 ⏰
\`\`\`http
GET /api/giftcard/:id
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCard": {
      "id": "giftcard_id",
      "cardType": "physical",
      "brand": "Amazon",
      "amount": 100,
      "currency": "USD",
      "estimatedValue": 82000,
      "actualValue": 82000,
      "rate": 0.82,
      "cardCode": "ABCD-****-****-MNOP",
      "status": "completed",
      "reference": "GC_REF_123456",
      "cardImage": "https://example.com/uploads/giftcard_image.jpg",
      "submittedAt": "2024-01-10T15:20:00Z",
      "validatedAt": "2024-01-10T16:30:00Z",
      "completedAt": "2024-01-10T18:45:00Z",
      "validatedBy": "admin_id",
      "notes": "Card validated successfully",
      "timeline": [
        {
          "status": "pending_validation",
          "timestamp": "2024-01-10T15:20:00Z",
          "description": "Gift card submitted for validation"
        },
        {
          "status": "validated",
          "timestamp": "2024-01-10T16:30:00Z",
          "description": "Gift card validated by admin"
        },
        {
          "status": "completed",
          "timestamp": "2024-01-10T18:45:00Z",
          "description": "Payment processed successfully"
        }
      ]
    }
  }
}
\`\`\`

#### 9.5 Export Gift Card History 🔐 ⏰
\`\`\`http
GET /api/giftcard/export
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?format=pdf&startDate=2024-01-01&endDate=2024-01-31&status=completed
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card history export generated successfully",
  "data": {
    "downloadUrl": "https://example.com/exports/giftcards_2024-01.pdf",
    "format": "pdf",
    "recordCount": 25,
    "generatedAt": "2024-01-15T10:30:00Z",
    "expiresAt": "2024-01-16T10:30:00Z"
  }
}
\`\`\`

#### 9.6 Get All Gift Card Rates (Admin) 👑
\`\`\`http
GET /api/giftcard/admin/rates
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCardRates": [
      {
        "id": "rate_id_1",
        "cardType": "physical",
        "brand": "Amazon",
        "cardName": "Amazon Gift Card",
        "currency": "USD",
        "minAmount": 25,
        "maxAmount": 500,
        "cardImage": "https://example.com/cards/amazon.png",
        "rateRanges": [
          {
            "minAmount": 25,
            "maxAmount": 100,
            "ratePerUnit": 0.82
          },
          {
            "minAmount": 101,
            "maxAmount": 500,
            "ratePerUnit": 0.85
          }
        ],
        "isActive": true,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
\`\`\`

#### 9.7 Create/Update Gift Card Rate (Admin) 👑
\`\`\`http
POST /api/giftcard/admin/rates
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
Content-Type: multipart/form-data
\`\`\`

**Request Body (Form Data):**
\`\`\`
cardType: physical
brand: Google Play
cardName: Google Play Gift Card
minAmount: 10
maxAmount: 200
rateRanges: [{"minAmount": 10, "maxAmount": 50, "ratePerUnit": 0.75}, {"minAmount": 51, "maxAmount": 200, "ratePerUnit": 0.78}]
cardImage: [file upload]
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card rate created successfully",
  "data": {
    "giftCardRate": {
      "id": "rate_id_2",
      "cardType": "physical",
      "brand": "Google Play",
      "cardName": "Google Play Gift Card",
      "currency": "USD",
      "minAmount": 10,
      "maxAmount": 200,
      "cardImage": "https://example.com/cards/googleplay.png",
      "rateRanges": [
        {
          "minAmount": 10,
          "maxAmount": 50,
          "ratePerUnit": 0.75
        },
        {
          "minAmount": 51,
          "maxAmount": 200,
          "ratePerUnit": 0.78
        }
      ],
      "isActive": true,
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 9.8 Delete Gift Card Rate (Admin) 👑
\`\`\`http
DELETE /api/giftcard/admin/rates/:cardType
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card rate deleted successfully"
}
\`\`\`

#### 9.9 Get All Gift Cards (Admin) 👑
\`\`\`http
GET /api/giftcard/admin/cards
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&status=pending_validation&brand=Amazon&userId=user_id
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCards": [
      {
        "id": "giftcard_id_1",
        "cardType": "physical",
        "brand": "Amazon",
        "amount": 100,
        "currency": "USD",
        "estimatedValue": 82000,
        "user": {
          "id": "user_id",
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@example.com"
        },
        "status": "pending_validation",
        "reference": "GC_REF_123456",
        "submittedAt": "2024-01-15T10:30:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 10,
      "totalItems": 200,
      "itemsPerPage": 20
    },
    "summary": {
      "totalPending": 25,
      "totalValidated": 150,
      "totalRejected": 25,
      "totalValue": 15000000
    }
  }
}
\`\`\`

#### 9.10 Get Gift Card by ID (Admin) 👑
\`\`\`http
GET /api/giftcard/admin/cards/:id
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCard": {
      "id": "giftcard_id",
      "cardType": "physical",
      "brand": "Amazon",
      "amount": 100,
      "currency": "USD",
      "estimatedValue": 82000,
      "rate": 0.82,
      "cardCode": "ABCD-EFGH-IJKL-MNOP",
      "cardPin": "1234",
      "expiryDate": "2025-12-31",
      "cardImage": "https://example.com/uploads/giftcard_image.jpg",
      "user": {
        "id": "user_id",
        "firstName": "John",
        "lastName": "Doe",
        "email": "john.doe@example.com",
        "phone": "+2348012345678"
      },
      "status": "pending_validation",
      "reference": "GC_REF_123456",
      "submittedAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 9.11 Validate Gift Card (Admin) 👑
\`\`\`http
POST /api/giftcard/admin/cards/:id/validate
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "isValid": true,
  "notes": "Card validated successfully",
  "customRate": 0.83
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card validated successfully",
  "data": {
    "giftCard": {
      "id": "giftcard_id",
      "status": "validated",
      "actualValue": 83000,
      "rate": 0.83,
      "validatedBy": "admin_id",
      "validatedAt": "2024-01-15T10:45:00Z",
      "notes": "Card validated successfully"
    }
  }
}
\`\`\`

#### 9.12 Process Payment (Admin) 👑
\`\`\`http
POST /api/giftcard/admin/cards/:id/process-payment
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Payment processed successfully",
  "data": {
    "giftCard": {
      "id": "giftcard_id",
      "status": "completed",
      "actualValue": 83000,
      "processedBy": "admin_id",
      "processedAt": "2024-01-15T10:50:00Z",
      "completedAt": "2024-01-15T10:50:00Z"
    }
  }
}
\`\`\`

---

## 10. Gift Card Buy

### Base Route: `/api/giftcard-buy`

#### 10.1 Get Available Gift Cards 🔓
\`\`\`http
GET /api/giftcard-buy/available
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCards": [
      {
        "id": "buy_giftcard_id_1",
        "brand": "Amazon",
        "cardName": "Amazon Gift Card",
        "cardImage": "https://example.com/cards/amazon_buy.png",
        "currency": "USD",
        "denominations": [25, 50, 100, 200],
        "priceRanges": [
          {
            "denomination": 25,
            "nairaPrice": 37500,
            "discount": 0
          },
          {
            "denomination": 50,
            "nairaPrice": 75000,
            "discount": 0
          },
          {
            "denomination": 100,
            "nairaPrice": 150000,
            "discount": 2
          },
          {
            "denomination": 200,
            "nairaPrice": 300000,
            "discount": 5
          }
        ],
        "isActive": true,
        "deliveryTime": "Instant",
        "description": "Digital Amazon Gift Card delivered instantly"
      }
    ]
  }
}
\`\`\`

#### 10.2 Get Gift Card by ID 🔓
\`\`\`http
GET /api/giftcard-buy/:giftCardId
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCard": {
      "id": "buy_giftcard_id_1",
      "brand": "Amazon",
      "cardName": "Amazon Gift Card",
      "cardImage": "https://example.com/cards/amazon_buy.png",
      "currency": "USD",
      "denominations": [25, 50, 100, 200],
      "priceRanges": [
        {
          "denomination": 25,
          "nairaPrice": 37500,
          "discount": 0
        },
        {
          "denomination": 50,
          "nairaPrice": 75000,
          "discount": 0
        },
        {
          "denomination": 100,
          "nairaPrice": 150000,
          "discount": 2
        },
        {
          "denomination": 200,
          "nairaPrice": 300000,
          "discount": 5
        }
      ],
      "isActive": true,
      "deliveryTime": "Instant",
      "description": "Digital Amazon Gift Card delivered instantly",
      "termsAndConditions": [
        "Gift card is non-refundable",
        "Valid for 1 year from purchase date",
        "Can be used on Amazon.com only"
      ]
    }
  }
}
\`\`\`

#### 10.3 Create Purchase Order 🔐
\`\`\`http
POST /api/giftcard-buy/purchase
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "giftCardId": "buy_giftcard_id_1",
  "denomination": 100
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Purchase order created successfully",
  "data": {
    "order": {
      "id": "purchase_order_id",
      "giftCardId": "buy_giftcard_id_1",
      "brand": "Amazon",
      "cardName": "Amazon Gift Card",
      "denomination": 100,
      "currency": "USD",
      "nairaPrice": 150000,
      "discount": 2,
      "finalPrice": 147000,
      "status": "pending_payment",
      "orderNumber": "GC_BUY_123456",
      "paymentDeadline": "2024-01-15T11:30:00Z",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 10.4 Get User Purchase Orders 🔐
\`\`\`http
GET /api/giftcard-buy/orders/my-orders
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&status=completed&brand=Amazon
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "orders": [
      {
        "id": "purchase_order_id_1",
        "brand": "Amazon",
        "cardName": "Amazon Gift Card",
        "denomination": 100,
        "currency": "USD",
        "nairaPrice": 150000,
        "finalPrice": 147000,
        "status": "completed",
        "orderNumber": "GC_BUY_123456",
        "cardCode": "ABCD-EFGH-IJKL-MNOP",
        "createdAt": "2024-01-10T15:20:00Z",
        "deliveredAt": "2024-01-10T15:25:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 3,
      "totalItems": 45,
      "itemsPerPage": 20
    }
  }
}
\`\`\`

#### 10.5 Get Purchase Order by ID 🔐
\`\`\`http
GET /api/giftcard-buy/orders/:orderId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "order": {
      "id": "purchase_order_id",
      "giftCardId": "buy_giftcard_id_1",
      "brand": "Amazon",
      "cardName": "Amazon Gift Card",
      "denomination": 100,
      "currency": "USD",
      "nairaPrice": 150000,
      "discount": 2,
      "finalPrice": 147000,
      "status": "completed",
      "orderNumber": "GC_BUY_123456",
      "cardCode": "ABCD-EFGH-IJKL-MNOP",
      "cardPin": null,
      "expiryDate": "2025-01-10T23:59:59Z",
      "instructions": "Redeem at Amazon.com checkout",
      "createdAt": "2024-01-10T15:20:00Z",
      "paidAt": "2024-01-10T15:22:00Z",
      "deliveredAt": "2024-01-10T15:25:00Z"
    }
  }
}
\`\`\`

#### 10.6 Cancel Purchase Order 🔐
\`\`\`http
PATCH /api/giftcard-buy/orders/:orderId/cancel
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Purchase order cancelled successfully",
  "data": {
    "order": {
      "id": "purchase_order_id",
      "status": "cancelled",
      "cancelledAt": "2024-01-15T10:45:00Z",
      "refundAmount": 147000,
      "refundStatus": "processing"
    }
  }
}
\`\`\`

#### 10.7 Get All Gift Card Buy Prices (Admin) 👑
\`\`\`http
GET /api/giftcard-buy/admin/prices
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "giftCardPrices": [
      {
        "id": "buy_price_id_1",
        "brand": "Amazon",
        "cardName": "Amazon Gift Card",
        "cardImage": "https://example.com/cards/amazon_buy.png",
        "currency": "USD",
        "priceRanges": [
          {
            "denomination": 25,
            "nairaPrice": 37500,
            "discount": 0
          },
          {
            "denomination": 50,
            "nairaPrice": 75000,
            "discount": 0
          }
        ],
        "isActive": true,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
\`\`\`

#### 10.8 Create Gift Card Buy Price (Admin) 👑
\`\`\`http
POST /api/giftcard-buy/admin/prices
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
Content-Type: multipart/form-data
\`\`\`

**Request Body (Form Data):**
\`\`\`
brand: Steam
cardName: Steam Wallet Gift Card
priceRanges: [{"denomination": 20, "nairaPrice": 30000, "discount": 0}, {"denomination": 50, "nairaPrice": 75000, "discount": 3}]
cardImage: [file upload]
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card buy price created successfully",
  "data": {
    "giftCardPrice": {
      "id": "buy_price_id_2",
      "brand": "Steam",
      "cardName": "Steam Wallet Gift Card",
      "cardImage": "https://example.com/cards/steam_buy.png",
      "currency": "USD",
      "priceRanges": [
        {
          "denomination": 20,
          "nairaPrice": 30000,
          "discount": 0
        },
        {
          "denomination": 50,
          "nairaPrice": 75000,
          "discount": 3
        }
      ],
      "isActive": true,
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 10.9 Update Gift Card Buy Price (Admin) 👑
\`\`\`http
PUT /api/giftcard-buy/admin/prices/:priceId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
Content-Type: multipart/form-data
\`\`\`

**Request Body (Form Data):**
\`\`\`
brand: Steam
cardName: Steam Wallet Gift Card
priceRanges: [{"denomination": 20, "nairaPrice": 32000, "discount": 0}, {"denomination": 50, "nairaPrice": 78000, "discount": 5}]
cardImage: [file upload]
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card buy price updated successfully",
  "data": {
    "giftCardPrice": {
      "id": "buy_price_id_2",
      "brand": "Steam",
      "cardName": "Steam Wallet Gift Card",
      "priceRanges": [
        {
          "denomination": 20,
          "nairaPrice": 32000,
          "discount": 0
        },
        {
          "denomination": 50,
          "nairaPrice": 78000,
          "discount": 5
        }
      ],
      "updatedAt": "2024-01-15T10:45:00Z"
    }
  }
}
\`\`\`

#### 10.10 Delete Gift Card Buy Price (Admin) 👑
\`\`\`http
DELETE /api/giftcard-buy/admin/prices/:priceId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card buy price deleted successfully"
}
\`\`\`

#### 10.11 Get All Purchase Orders (Admin) 👑
\`\`\`http
GET /api/giftcard-buy/admin/orders
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&status=pending_delivery&brand=Amazon&userId=user_id
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "orders": [
      {
        "id": "purchase_order_id_1",
        "brand": "Amazon",
        "cardName": "Amazon Gift Card",
        "denomination": 100,
        "finalPrice": 147000,
        "user": {
          "id": "user_id",
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@example.com"
        },
        "status": "pending_delivery",
        "orderNumber": "GC_BUY_123456",
        "createdAt": "2024-01-15T10:30:00Z",
        "paidAt": "2024-01-15T10:32:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 100,
      "itemsPerPage": 20
    }
  }
}
\`\`\`

#### 10.12 Process Delivery (Admin) 👑
\`\`\`http
POST /api/giftcard-buy/admin/orders/:orderId/deliver
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "cardCode": "ABCD-EFGH-IJKL-MNOP"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Gift card delivered successfully",
  "data": {
    "order": {
      "id": "purchase_order_id",
      "status": "completed",
      "cardCode": "ABCD-EFGH-IJKL-MNOP",
      "deliveredBy": "admin_id",
      "deliveredAt": "2024-01-15T10:45:00Z"
    }
  }
}
\`\`\`

---

## 11. Transaction Management

### Base Route: `/api/transaction`

#### 11.1 Get User Transactions 🔐
\`\`\`http
GET /api/transaction/user
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=all&status=completed&startDate=2024-01-01&endDate=2024-01-31&category=crypto
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": "tx_id_1",
        "type": "crypto_buy",
        "category": "crypto",
        "amount": 63000,
        "currency": "NGN",
        "cryptoAmount": 0.001,
        "cryptoCurrency": "BTC",
        "status": "completed",
        "description": "Bitcoin purchase",
        "reference": "CRYPTO_BUY_123456",
        "fee": 315,
        "createdAt": "2024-01-10T15:20:00Z",
        "completedAt": "2024-01-10T15:25:00Z"
      },
      {
        "id": "tx_id_2",
        "type": "bill_payment",
        "category": "bills",
        "amount": 5000,
        "currency": "NGN",
        "status": "completed",
        "description": "EKEDC electricity payment",
        "reference": "ELEC_REF_123456",
        "fee": 50,
        "createdAt": "2024-01-12T09:45:00Z",
        "completedAt": "2024-01-12T09:45:25Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 10,
      "totalItems": 200,
      "itemsPerPage": 20
    },
    "summary": {
      "totalAmount": 500000,
      "totalFees": 5000,
      "transactionCount": 200,
      "categories": {
        "crypto": 150000,
        "bills": 200000,
        "transfers": 100000,
        "giftcards": 50000
      }
    }
  }
}
\`\`\`

#### 11.2 Search Transactions 🔐
\`\`\`http
GET /api/transaction/user/search
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?query=bitcoin&type=crypto_buy&minAmount=50000&maxAmount=100000&startDate=2024-01-01
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": "tx_id_1",
        "type": "crypto_buy",
        "category": "crypto",
        "amount": 63000,
        "currency": "NGN",
        "cryptoAmount": 0.001,
        "cryptoCurrency": "BTC",
        "status": "completed",
        "description": "Bitcoin purchase",
        "reference": "CRYPTO_BUY_123456",
        "fee": 315,
        "createdAt": "2024-01-10T15:20:00Z",
        "completedAt": "2024-01-10T15:25:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 2,
      "totalItems": 25,
      "itemsPerPage": 20
    },
    "searchQuery": "bitcoin",
    "filters": {
      "type": "crypto_buy",
      "minAmount": 50000,
      "maxAmount": 100000,
      "startDate": "2024-01-01"
    }
  }
}
\`\`\`

#### 11.3 Export Transactions 🔐
\`\`\`http
GET /api/transaction/user/export
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?format=pdf&startDate=2024-01-01&endDate=2024-01-31&type=all&status=completed
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Transaction export generated successfully",
  "data": {
    "downloadUrl": "https://example.com/exports/transactions_2024-01.pdf",
    "format": "pdf",
    "recordCount": 200,
    "generatedAt": "2024-01-15T10:30:00Z",
    "expiresAt": "2024-01-16T10:30:00Z"
  }
}
\`\`\`

#### 11.4 Get Fraud Detection 🔐
\`\`\`http
GET /api/transaction/user/fraud-check
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "fraudCheck": {
      "riskScore": 25,
      "riskLevel": "low",
      "flags": [],
      "recommendations": [
        "Continue monitoring transaction patterns",
        "Enable 2FA for enhanced security"
      ],
      "lastChecked": "2024-01-15T10:30:00Z",
      "transactionVelocity": {
        "last24h": 5,
        "last7d": 23,
        "last30d": 87
      },
      "unusualPatterns": []
    }
  }
}
\`\`\`

#### 11.5 Get Transaction Details 🔐
\`\`\`http
GET /api/transaction/user/:transactionId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transaction": {
      "id": "tx_id_1",
      "type": "crypto_buy",
      "category": "crypto",
      "amount": 63000,
      "currency": "NGN",
      "cryptoAmount": 0.001,
      "cryptoCurrency": "BTC",
      "status": "completed",
      "description": "Bitcoin purchase",
      "reference": "CRYPTO_BUY_123456",
      "fee": 315,
      "rate": 63000000,
      "paymentMethod": "wallet",
      "metadata": {
        "userWalletAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "adminWalletAddress": "bc1qadmin2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "transactionHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
      },
      "createdAt": "2024-01-10T15:20:00Z",
      "completedAt": "2024-01-10T15:25:00Z",
      "timeline": [
        {
          "status": "pending",
          "timestamp": "2024-01-10T15:20:00Z",
          "description": "Transaction initiated"
        },
        {
          "status": "processing",
          "timestamp": "2024-01-10T15:22:00Z",
          "description": "Payment confirmed, processing crypto transfer"
        },
        {
          "status": "completed",
          "timestamp": "2024-01-10T15:25:00Z",
          "description": "Crypto transferred successfully"
        }
      ]
    }
  }
}
\`\`\`

#### 11.6 Generate Receipt 🔐
\`\`\`http
GET /api/transaction/user/:transactionId/receipt
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Receipt generated successfully",
  "data": {
    "receipt": {
      "transactionId": "tx_id_1",
      "type": "crypto_buy",
      "amount": 63000,
      "currency": "NGN",
      "cryptoAmount": 0.001,
      "cryptoCurrency": "BTC",
      "fee": 315,
      "totalAmount": 63315,
      "status": "completed",
      "reference": "CRYPTO_BUY_123456",
      "completedAt": "2024-01-10T15:25:00Z",
      "receiptUrl": "https://example.com/receipts/tx_id_1.pdf",
      "receiptNumber": "RCP_123456"
    }
  }
}
\`\`\`

#### 11.7 Get All Transactions (Admin) 👑
\`\`\`http
GET /api/transaction/admin
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=all&status=completed&userId=user_id&startDate=2024-01-01&endDate=2024-01-31
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": "tx_id_1",
        "type": "crypto_buy",
        "category": "crypto",
        "amount": 63000,
        "currency": "NGN",
        "user": {
          "id": "user_id",
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@example.com"
        },
        "status": "completed",
        "reference": "CRYPTO_BUY_123456",
        "fee": 315,
        "createdAt": "2024-01-10T15:20:00Z",
        "completedAt": "2024-01-10T15:25:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 50,
      "totalItems": 1000,
      "itemsPerPage": 20
    },
    "summary": {
      "totalAmount": 50000000,
      "totalFees": 500000,
      "transactionCount": 1000,
      "averageAmount": 50000
    }
  }
}
\`\`\`

#### 11.8 Get Transaction Analytics (Admin) 👑
\`\`\`http
GET /api/transaction/admin/analytics
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?period=30d&groupBy=day&category=crypto
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "analytics": {
      "period": "30d",
      "totalTransactions": 5000,
      "totalVolume": 500000000,
      "totalFees": 5000000,
      "averageTransactionValue": 100000,
      "transactionsByCategory": {
        "crypto": 2000,
        "bills": 1500,
        "transfers": 1000,
        "giftcards": 500
      },
      "volumeByCategory": {
        "crypto": 300000000,
        "bills": 100000000,
        "transfers": 80000000,
        "giftcards": 20000000
      },
      "dailyData": [
        {
          "date": "2024-01-01",
          "transactions": 150,
          "volume": 15000000,
          "fees": 150000
        },
        {
          "date": "2024-01-02",
          "transactions": 180,
          "volume": 18000000,
          "fees": 180000
        }
      ],
      "topUsers": [
        {
          "userId": "user_id_1",
          "firstName": "John",
          "lastName": "Doe",
          "transactionCount": 50,
          "totalVolume": 5000000
        }
      ]
    }
  }
}
\`\`\`

---

## 12. Transaction PIN

### Base Route: `/api/transaction-pin`

#### 12.1 Setup Transaction PIN 🔐
\`\`\`http
POST /api/transaction-pin/setup
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "pin": "123456",
  "currentPassword": "SecurePass123!"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Transaction PIN setup successfully",
  "data": {
    "hasTransactionPin": true,
    "setupAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 12.2 Change Transaction PIN 🔐
\`\`\`http
PUT /api/transaction-pin/change
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currentPin": "123456",
  "newPin": "654321"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Transaction PIN changed successfully",
  "data": {
    "changedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 12.3 Verify Transaction PIN 🔐
\`\`\`http
POST /api/transaction-pin/verify
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "pin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Transaction PIN verified successfully",
  "data": {
    "verified": true,
    "verifiedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 12.4 Disable Transaction PIN 🔐
\`\`\`http
DELETE /api/transaction-pin/disable
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currentPassword": "SecurePass123!",
  "pin": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Transaction PIN disabled successfully",
  "data": {
    "hasTransactionPin": false,
    "disabledAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 12.5 Get PIN Status 🔐
\`\`\`http
GET /api/transaction-pin/status
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "hasTransactionPin": true,
    "setupAt": "2024-01-10T15:20:00Z",
    "lastUsed": "2024-01-15T09:45:00Z",
    "failedAttempts": 0,
    "isLocked": false,
    "lockExpiresAt": null
  }
}
\`\`\`

---

## 13. Six Digit Password

### Base Route: `/api/six-digit-password`

#### 13.1 Setup Six Digit Password 🔐
\`\`\`http
POST /api/six-digit-password/setup
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "sixDigitPassword": "123456",
  "currentPassword": "SecurePass123!"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Six digit password setup successfully",
  "data": {
    "hasSixDigitPassword": true,
    "setupAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 13.2 Verify Six Digit Password 🔐
\`\`\`http
POST /api/six-digit-password/verify
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "sixDigitPassword": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Six digit password verified successfully",
  "data": {
    "verified": true,
    "verifiedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 13.3 Disable Six Digit Password 🔐
\`\`\`http
DELETE /api/six-digit-password/disable
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "currentPassword": "SecurePass123!",
  "sixDigitPassword": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Six digit password disabled successfully",
  "data": {
    "hasSixDigitPassword": false,
    "disabledAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 13.4 Get Six Digit Password Status 🔐
\`\`\`http
GET /api/six-digit-password/status
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "hasSixDigitPassword": true,
    "setupAt": "2024-01-10T15:20:00Z",
    "lastUsed": "2024-01-15T09:45:00Z",
    "failedAttempts": 0,
    "isLocked": false,
    "lockExpiresAt": null
  }
}
\`\`\`

---

## 14. Session Management

### Base Route: `/api/session`

#### 14.1 Get Session Status 🔐
\`\`\`http
GET /api/session/status
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "session": {
      "id": "session_id",
      "userId": "user_id",
      "isActive": true,
      "lastActivity": "2024-01-15T10:30:00Z",
      "expiresAt": "2024-01-15T12:30:00Z",
      "timeoutDuration": 7200,
      "remainingTime": 6300,
      "deviceInfo": {
        "userAgent": "Mozilla/5.0...",
        "ipAddress": "192.168.1.100",
        "location": "Lagos, Nigeria"
      },
      "requiresReauth": false,
      "reauthReason": null
    }
  }
}
\`\`\`

#### 14.2 Extend Session 🔐
\`\`\`http
POST /api/session/extend
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Session extended successfully",
  "data": {
    "session": {
      "id": "session_id",
      "expiresAt": "2024-01-15T14:30:00Z",
      "extendedAt": "2024-01-15T10:30:00Z",
      "remainingTime": 14400
    }
  }
}
\`\`\`

#### 14.3 Invalidate All Sessions 🔐
\`\`\`http
DELETE /api/session/invalidate-all
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "All sessions invalidated successfully",
  "data": {
    "invalidatedSessions": 3,
    "invalidatedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 14.4 Initiate Re-authentication 🔐
\`\`\`http
POST /api/session/reauth/initiate
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "reason": "high_value_transaction",
  "context": {
    "transactionAmount": 100000,
    "transactionType": "crypto_buy"
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Re-authentication initiated",
  "data": {
    "reauthToken": "reauth_token_123456",
    "availableMethods": ["password", "biometric", "six_digit_password"],
    "expiresAt": "2024-01-15T10:35:00Z",
    "reason": "high_value_transaction"
  }
}
\`\`\`

#### 14.5 Biometric Challenge 🔐 🔒
\`\`\`http
POST /api/session/reauth/biometric/challenge
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "reauthToken": "reauth_token_123456",
  "biometricType": "fingerprint"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Biometric challenge initiated",
  "data": {
    "challengeId": "biometric_challenge_id",
    "biometricType": "fingerprint",
    "challenge": "base64_encoded_challenge_data",
    "expiresAt": "2024-01-15T10:32:00Z"
  }
}
\`\`\`

#### 14.6 Complete Re-authentication 🔐
\`\`\`http
POST /api/session/reauth/complete
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body (Password):**
\`\`\`json
{
  "reauthToken": "reauth_token_123456",
  "method": "password",
  "password": "SecurePass123!"
}
\`\`\`

**Request Body (Biometric):**
\`\`\`json
{
  "reauthToken": "reauth_token_123456",
  "method": "biometric",
  "challengeId": "biometric_challenge_id",
  "biometricResponse": "base64_encoded_biometric_response"
}
\`\`\`

**Request Body (Six Digit Password):**
\`\`\`json
{
  "reauthToken": "reauth_token_123456",
  "method": "six_digit_password",
  "sixDigitPassword": "123456"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Re-authentication completed successfully",
  "data": {
    "reauthenticated": true,
    "method": "password",
    "reauthenticatedAt": "2024-01-15T10:30:00Z",
    "validUntil": "2024-01-15T10:45:00Z"
  }
}
\`\`\`

---

## 15. KYC Management

### Base Route: `/api/kyc`

#### 15.1 Submit KYC Documents 🔐
\`\`\`http
POST /api/kyc/submit
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
Content-Type: multipart/form-data
\`\`\`

**Request Body (Form Data):**
\`\`\`
documentType: national_id
idNumber: 12345678901
firstName: John
lastName: Doe
dateOfBirth: 1990-01-01
address: 123 Main Street, Lagos, Nigeria
idDocument: [file upload]
proofOfAddress: [file upload]
selfie: [file upload]
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "KYC documents submitted successfully",
  "data": {
    "kycSubmission": {
      "id": "kyc_submission_id",
      "userId": "user_id",
      "documentType": "national_id",
      "idNumber": "****8901",
      "status": "pending_review",
      "submittedAt": "2024-01-15T10:30:00Z",
      "estimatedReviewTime": "24-48 hours",
      "documents": {
        "idDocument": {
          "filename": "id_document.jpg",
          "status": "uploaded"
        },
        "proofOfAddress": {
          "filename": "proof_of_address.pdf",
          "status": "uploaded"
        },
        "selfie": {
          "filename": "selfie.jpg",
          "status": "uploaded"
        }
      }
    }
  }
}
\`\`\`

#### 15.2 Get KYC Status 🔐
\`\`\`http
GET /api/kyc/status
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "kyc": {
      "userId": "user_id",
      "currentLevel": "intermediate",
      "status": "approved",
      "submittedAt": "2024-01-10T15:20:00Z",
      "reviewedAt": "2024-01-11T14:30:00Z",
      "reviewedBy": "admin_id",
      "documents": {
        "idDocument": {
          "type": "national_id",
          "number": "****8901",
          "status": "approved",
          "verifiedAt": "2024-01-11T14:30:00Z"
        },
        "proofOfAddress": {
          "type": "utility_bill",
          "status": "approved",
          "verifiedAt": "2024-01-11T14:30:00Z"
        },
        "selfie": {
          "status": "approved",
          "verifiedAt": "2024-01-11T14:30:00Z"
        }
      },
      "limits": {
        "dailyTransactionLimit": 1000000,
        "monthlyTransactionLimit": 10000000,
        "singleTransactionLimit": 500000,
        "cryptoTradingEnabled": true,
        "internationalTransfersEnabled": true,
        "withdrawalLimit": 500000
      },
      "nextLevel": {
        "level": "advanced",
        "requirements": [
          "Bank statement (last 3 months)",
          "Proof of income",
          "Video verification call"
        ],
        "benefits": [
          "Increased transaction limits",
          "Priority customer support",
          "Access to premium features"
        ]
      }
    }
  }
}
\`\`\`

#### 15.3 Get KYC Requirements 🔐
\`\`\`http
GET /api/kyc/requirements
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "kycLevels": {
      "basic": {
        "level": "basic",
        "requirements": [
          "Email verification",
          "Phone verification",
          "Basic profile information"
        ],
        "limits": {
          "dailyTransactionLimit": 100000,
          "monthlyTransactionLimit": 1000000,
          "singleTransactionLimit": 50000,
          "cryptoTradingEnabled": false,
          "internationalTransfersEnabled": false
        },
        "features": [
          "Wallet funding",
          "Bill payments",
          "Local transfers"
        ]
      },
      "intermediate": {
        "level": "intermediate",
        "requirements": [
          "Government-issued ID",
          "Proof of address",
          "Selfie verification"
        ],
        "limits": {
          "dailyTransactionLimit": 1000000,
          "monthlyTransactionLimit": 10000000,
          "singleTransactionLimit": 500000,
          "cryptoTradingEnabled": true,
          "internationalTransfersEnabled": true
        },
        "features": [
          "All basic features",
          "Crypto trading",
          "Gift card trading",
          "International transfers"
        ]
      },
      "advanced": {
        "level": "advanced",
        "requirements": [
          "Bank statement (last 3 months)",
          "Proof of income",
          "Video verification call"
        ],
        "limits": {
          "dailyTransactionLimit": 5000000,
          "monthlyTransactionLimit": 50000000,
          "singleTransactionLimit": 2000000,
          "cryptoTradingEnabled": true,
          "internationalTransfersEnabled": true
        },
        "features": [
          "All intermediate features",
          "Premium customer support",
          "Advanced trading features",
          "Higher transaction limits"
        ]
      }
    }
  }
}
\`\`\`

#### 15.4 Update KYC Information 🔐
\`\`\`http
PUT /api/kyc/update
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "firstName": "John",
  "lastName": "Doe",
  "dateOfBirth": "1990-01-01",
  "address": "456 New Street, Lagos, Nigeria",
  "occupation": "Software Engineer",
  "sourceOfIncome": "Employment"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "KYC information updated successfully",
  "data": {
    "kyc": {
      "id": "kyc_id",
      "firstName": "John",
      "lastName": "Doe",
      "dateOfBirth": "1990-01-01",
      "address": "456 New Street, Lagos, Nigeria",
      "occupation": "Software Engineer",
      "sourceOfIncome": "Employment",
      "updatedAt": "2024-01-15T10:30:00Z",
      "requiresReview": true
    }
  }
}
\`\`\`

#### 15.5 Get All KYC Submissions (Admin) 👑
\`\`\`http
GET /api/kyc/admin/submissions
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&status=pending_review&level=intermediate&startDate=2024-01-01
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "submissions": [
      {
        "id": "kyc_submission_id_1",
        "user": {
          "id": "user_id",
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@example.com"
        },
        "documentType": "national_id",
        "idNumber": "****8901",
        "status": "pending_review",
        "submittedAt": "2024-01-15T10:30:00Z",
        "documents": {
          "idDocument": {
            "filename": "id_document.jpg",
            "url": "https://example.com/documents/id_document.jpg"
          },
          "proofOfAddress": {
            "filename": "proof_of_address.pdf",
            "url": "https://example.com/documents/proof_of_address.pdf"
          },
          "selfie": {
            "filename": "selfie.jpg",
            "url": "https://example.com/documents/selfie.jpg"
          }
        }
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 10,
      "totalItems": 200,
      "itemsPerPage": 20
    },
    "summary": {
      "totalPending": 50,
      "totalApproved": 120,
      "totalRejected": 30
    }
  }
}
\`\`\`

#### 15.6 Review KYC Submission (Admin) 👑
\`\`\`http
POST /api/kyc/admin/submissions/:submissionId/review
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "status": "approved", // "approved" or "rejected"
  "level": "intermediate", // "basic", "intermediate", "advanced"
  "notes": "All documents verified successfully",
  "documentReviews": {
    "idDocument": {
      "status": "approved",
      "notes": "Clear and valid ID document"
    },
    "proofOfAddress": {
      "status": "approved",
      "notes": "Recent utility bill verified"
    },
    "selfie": {
      "status": "approved",
      "notes": "Selfie matches ID document"
    }
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "KYC submission reviewed successfully",
  "data": {
    "submission": {
      "id": "kyc_submission_id",
      "status": "approved",
      "level": "intermediate",
      "reviewedBy": "admin_id",
      "reviewedAt": "2024-01-15T10:45:00Z",
      "notes": "All documents verified successfully",
      "documentReviews": {
        "idDocument": {
          "status": "approved",
          "notes": "Clear and valid ID document"
        },
        "proofOfAddress": {
          "status": "approved",
          "notes": "Recent utility bill verified"
        },
        "selfie": {
          "status": "approved",
          "notes": "Selfie matches ID document"
        }
      }
    }
  }
}
\`\`\`

#### 15.7 Get KYC Analytics (Admin) 👑
\`\`\`http
GET /api/kyc/admin/analytics
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?period=30d&groupBy=day
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "analytics": {
      "period": "30d",
      "totalSubmissions": 500,
      "approvalRate": 85.6,
      "averageReviewTime": "18 hours",
      "submissionsByLevel": {
        "basic": 200,
        "intermediate": 250,
        "advanced": 50
      },
      "statusDistribution": {
        "pending_review": 50,
        "approved": 400,
        "rejected": 50
      },
      "dailySubmissions": [
        {
          "date": "2024-01-01",
          "submissions": 15,
          "approved": 12,
          "rejected": 2,
          "pending": 1
        }
      ],
      "rejectionReasons": [
        {
          "reason": "Blurry document image",
          "count": 20
        },
        {
          "reason": "Expired document",
          "count": 15
        }
      ]
    }
  }
}
\`\`\`

---

## 16. Notifications

### Base Route: `/api/notification`

#### 16.1 Get User Notifications 🔐
\`\`\`http
GET /api/notification/user
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?page=1&limit=20&type=transaction&status=unread&priority=high
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "notifications": [
      {
        "id": "notification_id_1",
        "type": "transaction",
        "title": "Transaction Successful",
        "message": "Your Bitcoin purchase of ₦63,000 was completed successfully.",
        "priority": "medium",
        "status": "unread",
        "actionUrl": "/transactions/tx_id_1",
        "imageUrl": "https://example.com/icons/bitcoin.png",
        "metadata": {
          "transactionId": "tx_id_1",
          "amount": 63000,
          "currency": "BTC"
        },
        "createdAt": "2024-01-15T10:30:00Z"
      },
      {
        "id": "notification_id_2",
        "type": "security",
        "title": "New Login Detected",
        "message": "A new login was detected from Lagos, Nigeria on Chrome browser.",
        "priority": "high",
        "status": "unread",
        "actionUrl": "/settings/security",
        "imageUrl": "https://example.com/icons/security.png",
        "metadata": {
          "location": "Lagos, Nigeria",
          "device": "Chrome on Windows",
          "ipAddress": "192.168.1.100"
        },
        "createdAt": "2024-01-15T09:45:00Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 87,
      "itemsPerPage": 20
    },
    "summary": {
      "totalUnread": 15,
      "totalRead": 72,
      "highPriority": 3,
      "mediumPriority": 10,
      "lowPriority": 2
    }
  }
}
\`\`\`

#### 16.2 Get Notification Settings 🔐
\`\`\`http
GET /api/notification/user/settings
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "settings": {
      "push": {
        "enabled": true,
        "transactionAlerts": true,
        "securityAlerts": true,
        "promotions": false,
        "appUpdates": true
      },
      "email": {
        "enabled": true,
        "transactionAlerts": true,
        "securityAlerts": true,
        "promotions": false,
        "appUpdates": false,
        "weeklyDigest": true
      },
      "sms": {
        "enabled": false,
        "transactionAlerts": false,
        "securityAlerts": true,
        "promotions": false
      },
      "inApp": {
        "enabled": true,
        "sound": true,
        "vibration": true
      },
      "quietHours": {
        "enabled": true,
        "startTime": "22:00",
        "endTime": "07:00",
        "timezone": "Africa/Lagos"
      }
    }
  }
}
\`\`\`

#### 16.3 Update Notification Settings 🔐
\`\`\`http
PUT /api/notification/user/settings
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "push": {
    "enabled": true,
    "transactionAlerts": true,
    "securityAlerts": true,
    "promotions": false,
    "appUpdates": true
  },
  "email": {
    "enabled": true,
    "transactionAlerts": true,
    "securityAlerts": true,
    "promotions": false,
    "weeklyDigest": false
  },
  "sms": {
    "enabled": true,
    "transactionAlerts": false,
    "securityAlerts": true
  },
  "quietHours": {
    "enabled": true,
    "startTime": "23:00",
    "endTime": "06:00"
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Notification settings updated successfully",
  "data": {
    "settings": {
      "push": {
        "enabled": true,
        "transactionAlerts": true,
        "securityAlerts": true,
        "promotions": false,
        "appUpdates": true
      },
      "email": {
        "enabled": true,
        "transactionAlerts": true,
        "securityAlerts": true,
        "promotions": false,
        "weeklyDigest": false
      },
      "sms": {
        "enabled": true,
        "transactionAlerts": false,
        "securityAlerts": true
      },
      "quietHours": {
        "enabled": true,
        "startTime": "23:00",
        "endTime": "06:00"
      },
      "updatedAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 16.4 Mark Notification as Read 🔐
\`\`\`http
PUT /api/notification/user/:notificationId/read
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Notification marked as read",
  "data": {
    "notification": {
      "id": "notification_id_1",
      "status": "read",
      "readAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 16.5 Mark All Notifications as Read 🔐
\`\`\`http
PUT /api/notification/user/mark-all-read
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "All notifications marked as read",
  "data": {
    "markedCount": 15,
    "markedAt": "2024-01-15T10:30:00Z"
  }
}
\`\`\`

#### 16.6 Delete Notification 🔐
\`\`\`http
DELETE /api/notification/user/:notificationId
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Notification deleted successfully"
}
\`\`\`

#### 16.7 Register Device Token 🔐
\`\`\`http
POST /api/notification/user/register-device
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "token": "device_push_token_12345",
  "platform": "ios", // "ios", "android", "web"
  "deviceInfo": {
    "deviceId": "device_unique_id",
    "deviceName": "iPhone 12 Pro",
    "osVersion": "15.0",
    "appVersion": "1.2.0"
  },
  "topics": ["transactions", "security", "promotions"]
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Device registered successfully",
  "data": {
    "deviceToken": {
      "id": "device_token_id",
      "token": "device_push_token_12345",
      "platform": "ios",
      "deviceInfo": {
        "deviceId": "device_unique_id",
        "deviceName": "iPhone 12 Pro",
        "osVersion": "15.0",
        "appVersion": "1.2.0"
      },
      "topics": ["transactions", "security", "promotions"],
      "registeredAt": "2024-01-15T10:30:00Z",
      "isActive": true
    }
  }
}
\`\`\`

#### 16.8 Unregister Device Token 🔐
\`\`\`http
DELETE /api/notification/user/device-token
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "token": "device_push_token_12345"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Device token unregistered successfully"
}
\`\`\`

#### 16.9 Get Notification Stats (Admin) 👑
\`\`\`http
GET /api/notification/admin/stats
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Query Parameters:**
\`\`\`
?startDate=2024-01-01&endDate=2024-01-31
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "stats": {
      "period": {
        "startDate": "2024-01-01",
        "endDate": "2024-01-31"
      },
      "totalNotifications": 50000,
      "deliveryStats": {
        "push": {
          "sent": 30000,
          "delivered": 28500,
          "failed": 1500,
          "deliveryRate": 95.0
        },
        "email": {
          "sent": 15000,
          "delivered": 14700,
          "failed": 300,
          "deliveryRate": 98.0
        },
        "sms": {
          "sent": 5000,
          "delivered": 4900,
          "failed": 100,
          "deliveryRate": 98.0
        }
      },
      "notificationsByType": {
        "transaction": 25000,
        "security": 10000,
        "promotion": 8000,
        "system": 5000,
        "kyc": 2000
      },
      "engagementStats": {
        "openRate": 65.5,
        "clickRate": 12.3,
        "unsubscribeRate": 0.8
      },
      "topPerformingNotifications": [
        {
          "title": "Transaction Successful",
          "type": "transaction",
          "sent": 5000,
          "openRate": 85.2,
          "clickRate": 25.6
        }
      ]
    }
  }
}
\`\`\`

#### 16.10 Send Bulk Notification (Admin) 👑
\`\`\`http
POST /api/notification/admin/bulk
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "userIds": ["user_id_1", "user_id_2", "user_id_3"],
  "title": "System Maintenance Notice",
  "message": "We will be performing scheduled maintenance on January 20th from 2:00 AM to 4:00 AM WAT.",
  "type": "system",
  "priority": "high",
  "channels": {
    "push": true,
    "email": true,
    "sms": false
  },
  "actionUrl": "/maintenance-notice",
  "imageUrl": "https://example.com/icons/maintenance.png",
  "scheduledFor": "2024-01-19T18:00:00Z"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Bulk notification scheduled successfully",
  "data": {
    "notification": {
      "id": "bulk_notification_id",
      "title": "System Maintenance Notice",
      "message": "We will be performing scheduled maintenance on January 20th from 2:00 AM to 4:00 AM WAT.",
      "type": "system",
      "priority": "high",
      "targetUsers": 3,
      "channels": {
        "push": true,
        "email": true,
        "sms": false
      },
      "status": "scheduled",
      "scheduledFor": "2024-01-19T18:00:00Z",
      "createdAt": "2024-01-15T10:30:00Z",
      "createdBy": "admin_id"
    }
  }
}
\`\`\`

#### 16.11 Create Single Notification (Admin) 👑
\`\`\`http
POST /api/notification/admin
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "userId": "user_id",
  "title": "KYC Approved",
  "message": "Your KYC verification has been approved. You can now access all platform features.",
  "type": "kyc",
  "priority": "medium",
  "channels": {
    "push": true,
    "email": true,
    "sms": false
  },
  "actionUrl": "/kyc/status",
  "imageUrl": "https://example.com/icons/kyc-approved.png"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Notification created successfully",
  "data": {
    "notification": {
      "id": "notification_id",
      "userId": "user_id",
      "title": "KYC Approved",
      "message": "Your KYC verification has been approved. You can now access all platform features.",
      "type": "kyc",
      "priority": "medium",
      "channels": {
        "push": true,
        "email": true,
        "sms": false
      },
      "status": "sent",
      "createdAt": "2024-01-15T10:30:00Z",
      "createdBy": "admin_id"
    }
  }
}
\`\`\`

#### 16.12 Send App Update Notification (Admin) 👑
\`\`\`http
POST /api/notification/admin/app-update
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "version": "1.3.0",
  "platform": "ios", // "ios", "android", "web"
  "isForceUpdate": false,
  "releaseNotes": "Bug fixes and performance improvements",
  "downloadUrl": "https://apps.apple.com/app/greatminds",
  "userIds": ["user_id_1", "user_id_2"]
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "App update notification sent successfully",
  "data": {
    "notification": {
      "id": "app_update_notification_id",
      "version": "1.3.0",
      "platform": "ios",
      "isForceUpdate": false,
      "targetUsers": 2,
      "sentAt": "2024-01-15T10:30:00Z"
    }
  }
}
\`\`\`

#### 16.13 Send Topic Notification (Admin) 👑
\`\`\`http
POST /api/notification/admin/topic
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer jwt_admin_access_token
\`\`\`

**Request Body:**
\`\`\`json
{
  "topic": "crypto_traders",
  "title": "Bitcoin Price Alert",
  "message": "Bitcoin has reached a new all-time high of $45,000!",
  "data": {
    "price": 45000,
    "change": "+5.2%",
    "currency": "BTC"
  },
  "imageUrl": "https://example.com/icons/bitcoin-alert.png"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Topic notification sent successfully",
  "data": {
    "notification": {
      "id": "topic_notification_id",
      "topic": "crypto_traders",
      "title": "Bitcoin Price Alert",
      "message": "Bitcoin has reached a new all-time high of $45,000!",
      "sentAt": "2024-01-15T10:30:00Z",
      "estimatedReach": 1500
    }
  }
}
\`\`\`

---

## 17. Support System

### Base Route: `/api/support`

#### 17.1 Get FAQs 🔓

\`\`\`http
GET /api/support/faqs
\`\`\`

**Query Parameters:**
- `category` (optional): Filter by category
- `limit` (optional): Number of FAQs to return (default: 20)
- `page` (optional): Page number for pagination

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "faqs": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "question": "How do I reset my password?",
        "answer": "You can reset your password by clicking 'Forgot Password' on the login page...",
        "category": "account",
        "tags": ["password", "reset", "security"],
        "views": 1250,
        "helpful": 45,
        "notHelpful": 3,
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 98,
      "hasNext": true,
      "hasPrev": false
    }
  }
}
\`\`\`

#### 17.2 Get FAQ Categories 🔓

\`\`\`http
GET /api/support/faqs/categories
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": [
    {
      "name": "account",
      "displayName": "Account Management",
      "count": 25,
      "icon": "user"
    },
    {
      "name": "payments",
      "displayName": "Payments & Transactions",
      "count": 32,
      "icon": "credit-card"
    },
    {
      "name": "security",
      "displayName": "Security & Privacy",
      "count": 18,
      "icon": "shield"
    }
  ]
}
\`\`\`

#### 17.3 Create Support Ticket 🔐

\`\`\`http
POST /api/support/tickets
\`\`\`

**Headers:**
\`\`\`
Authorization: Bearer <user_token>
Content-Type: multipart/form-data
\`\`\`

**Request Body:**
\`\`\`json
{
  "subject": "Unable to complete crypto withdrawal",
  "description": "I've been trying to withdraw Bitcoin for the past 2 hours but the transaction keeps failing...",
  "category": "crypto",
  "priority": "high",
  "attachments": ["file1.jpg", "file2.pdf"]
}
\`\`\`

**Validation Rules:**
- `subject`: Required, max 200 characters
- `description`: Required, max 2000 characters
- `category`: Required, enum: ["general", "account", "crypto", "bills", "giftcard", "technical"]
- `priority`: Optional, enum: ["low", "medium", "high", "urgent"], default: "medium"
- `attachments`: Optional, max 5 files, 10MB each

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Support ticket created successfully",
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "ticketNumber": "TKT-2023-001234",
    "subject": "Unable to complete crypto withdrawal",
    "status": "open",
    "priority": "high",
    "category": "crypto",
    "createdAt": "2023-09-06T10:30:00.000Z",
    "estimatedResponse": "2023-09-06T14:30:00.000Z"
  }
}
\`\`\`

#### 17.4 Get User Tickets 🔐

\`\`\`http
GET /api/support/tickets
\`\`\`

**Query Parameters:**
- `status` (optional): Filter by status ["open", "in_progress", "resolved", "closed"]
- `category` (optional): Filter by category
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "tickets": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "ticketNumber": "TKT-2023-001234",
        "subject": "Unable to complete crypto withdrawal",
        "status": "in_progress",
        "priority": "high",
        "category": "crypto",
        "lastReply": "2023-09-06T12:15:00.000Z",
        "assignedTo": {
          "name": "John Support",
          "avatar": "https://example.com/avatar.jpg"
        },
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 3,
      "totalItems": 12
    }
  }
}
\`\`\`

#### 17.5 Reply to Ticket 🔐

\`\`\`http
POST /api/support/tickets/:ticketId/reply
\`\`\`

**Request Body:**
\`\`\`json
{
  "message": "I've tried the suggested solution but it's still not working...",
  "attachments": ["screenshot.png"]
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Reply sent successfully",
  "data": {
    "replyId": "64f8a1b2c3d4e5f6a7b8c9d1",
    "message": "I've tried the suggested solution but it's still not working...",
    "createdAt": "2023-09-06T14:30:00.000Z"
  }
}
\`\`\`

#### 17.6 Rate Ticket Resolution 🔐

\`\`\`http
POST /api/support/tickets/:ticketId/rate
\`\`\`

**Request Body:**
\`\`\`json
{
  "rating": 5,
  "feedback": "Excellent support! Issue resolved quickly."
}
\`\`\`

**Validation Rules:**
- `rating`: Required, integer 1-5
- `feedback`: Optional, max 500 characters

---

## 18. Activity Logs

### Base Route: `/api/activity`

#### 18.1 Get User Activity Logs 🔐

\`\`\`http
GET /api/activity/user
\`\`\`

**Query Parameters:**
- `category` (optional): Filter by category ["auth", "wallet", "crypto", "giftcard", "bill", "profile", "security"]
- `action` (optional): Filter by specific action
- `status` (optional): Filter by status ["success", "failed", "pending"]
- `startDate` (optional): Start date (ISO 8601)
- `endDate` (optional): End date (ISO 8601)
- `page` (optional): Page number
- `limit` (optional): Items per page (max 100)

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "logs": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "action": "CRYPTO_BUY",
        "category": "crypto",
        "description": "Purchased 0.001 BTC for ₦45,000",
        "status": "success",
        "ipAddress": "192.168.1.1",
        "location": {
          "country": "Nigeria",
          "city": "Lagos",
          "region": "Lagos State"
        },
        "metadata": {
          "amount": 45000,
          "currency": "BTC",
          "quantity": 0.001,
          "reference": "BTC_BUY_001234"
        },
        "riskScore": 15,
        "flagged": false,
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 25,
      "totalItems": 248
    },
    "summary": {
      "totalActivities": 248,
      "successfulActivities": 235,
      "failedActivities": 13,
      "flaggedActivities": 2,
      "averageRiskScore": 12.5
    }
  }
}
\`\`\`

#### 18.2 Search Activity Logs 🔐

\`\`\`http
GET /api/activity/user/search
\`\`\`

**Query Parameters:**
- `q`: Search term (required)
- `category` (optional): Filter by category
- `limit` (optional): Max results (default: 50)

**Validation Rules:**
- `q`: Required, min 3 characters

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "results": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "action": "CRYPTO_BUY",
        "description": "Purchased 0.001 BTC for ₦45,000",
        "createdAt": "2023-09-06T10:30:00.000Z",
        "relevanceScore": 0.95
      }
    ],
    "totalResults": 15,
    "searchTerm": "bitcoin purchase"
  }
}
\`\`\`

#### 18.3 Export Activity Logs 🔐

\`\`\`http
GET /api/activity/user/export
\`\`\`

**Query Parameters:**
- `format`: Export format ["csv", "pdf"] (default: "csv")
- `startDate` (optional): Start date
- `endDate` (optional): End date
- `category` (optional): Filter by category

**Response:**
- **CSV**: Returns CSV file download
- **PDF**: Returns PDF file download

#### 18.4 Get Fraud Detection Analysis 🔐

\`\`\`http
GET /api/activity/user/fraud-check
\`\`\`

**Query Parameters:**
- `timeWindow` (optional): Hours to analyze (default: 24)

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "riskLevel": "low",
    "riskScore": 25,
    "flags": [
      {
        "type": "unusual_location",
        "severity": "medium",
        "description": "Login from new location: Abuja",
        "timestamp": "2023-09-06T10:30:00.000Z"
      }
    ],
    "recommendations": [
      "Enable two-factor authentication",
      "Review recent login locations"
    ],
    "analysisWindow": "24 hours",
    "activitiesAnalyzed": 15
  }
}
\`\`\`

---

## 19. Referral System

### Base Route: `/api/referral`

#### 19.1 Get Referral Statistics 🔐

\`\`\`http
GET /api/referral/stats
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "totalReferrals": 25,
    "completedReferrals": 18,
    "pendingReferrals": 7,
    "totalEarnings": 45000,
    "thisMonthEarnings": 12000,
    "referralCode": "GREAT_JOHN123",
    "conversionRate": 72,
    "levels": {
      "level1": {
        "count": 18,
        "earnings": 36000
      },
      "level2": {
        "count": 7,
        "earnings": 9000
      }
    },
    "topReferrals": [
      {
        "userId": "64f8a1b2c3d4e5f6a7b8c9d0",
        "username": "jane_doe",
        "joinDate": "2023-08-15T10:30:00.000Z",
        "status": "completed",
        "earnings": 2500
      }
    ]
  }
}
\`\`\`

#### 19.2 Get Referral History 🔐

\`\`\`http
GET /api/referral/history
\`\`\`

**Query Parameters:**
- `status` (optional): Filter by status ["pending", "completed", "cancelled"]
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "referrals": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "referredUser": {
          "username": "jane_doe",
          "avatar": "https://example.com/avatar.jpg"
        },
        "status": "completed",
        "bonusAwarded": 2500,
        "level": 1,
        "completedAt": "2023-08-20T10:30:00.000Z",
        "metadata": {
          "signupDate": "2023-08-15T10:30:00.000Z",
          "firstTransactionDate": "2023-08-16T14:20:00.000Z",
          "qualifyingAmount": 50000
        }
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 3,
      "totalItems": 25
    }
  }
}
\`\`\`

#### 19.3 Generate Referral Link 🔐

\`\`\`http
GET /api/referral/generate-link
\`\`\`

**Query Parameters:**
- `campaign` (optional): Campaign identifier

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "referralCode": "GREAT_JOHN123",
    "referralLink": "https://greatminds.ng/signup?ref=GREAT_JOHN123",
    "qrCode": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
    "shareText": "Join GreatMinds and get ₦2,500 bonus! Use my referral code: GREAT_JOHN123",
    "socialLinks": {
      "whatsapp": "https://wa.me/?text=Join%20GreatMinds...",
      "twitter": "https://twitter.com/intent/tweet?text=Join%20GreatMinds...",
      "facebook": "https://www.facebook.com/sharer/sharer.php?u=..."
    }
  }
}
\`\`\`

#### 19.4 Get Referral Dashboard 🔐

\`\`\`http
GET /api/referral/dashboard
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "stats": {
      "totalReferrals": 25,
      "completedReferrals": 18,
      "totalEarnings": 45000,
      "thisMonthEarnings": 12000
    },
    "recentReferrals": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "referredUser": {
          "username": "jane_doe"
        },
        "status": "completed",
        "bonusAwarded": 2500,
        "completedAt": "2023-08-20T10:30:00.000Z"
      }
    ]
  }
}
\`\`\`

---

## 20. Promo Codes

### Base Route: `/api/promo`

#### 20.1 Validate Promo Code 🔐

\`\`\`http
POST /api/promo/user/validate
\`\`\`

**Request Body:**
\`\`\`json
{
  "code": "WELCOME50",
  "service": "crypto",
  "amount": 100000
}
\`\`\`

**Validation Rules:**
- `code`: Required, string
- `service`: Required, enum: ["crypto", "bills", "giftcard", "transfer", "all"]
- `amount`: Required, number > 0

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "isValid": true,
    "discountAmount": 5000,
    "bonusAmount": 0,
    "finalAmount": 95000,
    "promoDetails": {
      "code": "WELCOME50",
      "type": "percentage",
      "value": 5,
      "description": "5% discount on crypto purchases",
      "validUntil": "2023-12-31T23:59:59.000Z",
      "maxDiscount": 10000,
      "minAmount": 50000
    }
  }
}
\`\`\`

#### 20.2 Apply Promo Code 🔐

\`\`\`http
POST /api/promo/user/apply
\`\`\`

**Request Body:**
\`\`\`json
{
  "code": "WELCOME50",
  "service": "crypto",
  "amount": 100000,
  "transactionId": "64f8a1b2c3d4e5f6a7b8c9d0"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Promo code applied successfully",
  "data": {
    "discountApplied": 5000,
    "originalAmount": 100000,
    "finalAmount": 95000,
    "promoCode": "WELCOME50",
    "usageCount": 1,
    "remainingUses": 0
  }
}
\`\`\`

#### 20.3 Create Promo Code 👑

\`\`\`http
POST /api/promo/admin
\`\`\`

**Request Body:**
\`\`\`json
{
  "code": "BLACKFRIDAY2023",
  "type": "percentage",
  "value": 15,
  "description": "Black Friday 15% discount",
  "usageLimit": 1000,
  "userLimit": 1,
  "minAmount": 10000,
  "maxDiscount": 50000,
  "validFrom": "2023-11-24T00:00:00.000Z",
  "validUntil": "2023-11-27T23:59:59.000Z",
  "applicableServices": ["crypto", "giftcard"]
}
\`\`\`

**Validation Rules:**
- `code`: Required, unique, uppercase, 3-20 characters
- `type`: Required, enum: ["percentage", "fixed", "bonus", "cashback"]
- `value`: Required, number > 0
- `description`: Required, max 200 characters
- `validUntil`: Required, future date

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Promo code created successfully",
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "code": "BLACKFRIDAY2023",
    "type": "percentage",
    "value": 15,
    "isActive": true,
    "usageCount": 0,
    "createdAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 20.4 Get All Promo Codes 👑

\`\`\`http
GET /api/promo/admin
\`\`\`

**Query Parameters:**
- `status` (optional): Filter by status ["active", "inactive", "expired"]
- `type` (optional): Filter by type
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "promoCodes": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "code": "BLACKFRIDAY2023",
        "type": "percentage",
        "value": 15,
        "description": "Black Friday 15% discount",
        "isActive": true,
        "usageCount": 245,
        "usageLimit": 1000,
        "validUntil": "2023-11-27T23:59:59.000Z",
        "createdBy": {
          "name": "Admin User",
          "email": "admin@greatminds.ng"
        },
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 48
    }
  }
}
\`\`\`

---

## 21. Cashback System

### Base Route: `/api/cashback`

#### 21.1 Get Cashback History 🔐

\`\`\`http
GET /api/cashback/user/history
\`\`\`

**Query Parameters:**
- `service` (optional): Filter by service ["crypto", "bills", "giftcard", "transfer"]
- `status` (optional): Filter by status ["pending", "credited", "cancelled"]
- `startDate` (optional): Start date
- `endDate` (optional): End date
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "cashbacks": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "transactionId": "64f8a1b2c3d4e5f6a7b8c9d1",
        "service": "crypto",
        "transactionAmount": 100000,
        "cashbackRate": 2.5,
        "cashbackAmount": 2500,
        "status": "credited",
        "creditedAt": "2023-09-06T12:30:00.000Z",
        "metadata": {
          "campaignId": "CRYPTO_CASHBACK_Q4",
          "tier": "gold"
        },
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "summary": {
      "totalCashback": 25000,
      "pendingCashback": 5000,
      "creditedCashback": 20000,
      "thisMonthCashback": 7500
    },
    "pagination": {
      "currentPage": 1,
      "totalPages": 8,
      "totalItems": 76
    }
  }
}
\`\`\`

#### 21.2 Create Cashback Rule 👑

\`\`\`http
POST /api/cashback/admin/rules
\`\`\`

**Request Body:**
\`\`\`json
{
  "name": "Crypto Purchase Cashback",
  "service": "crypto",
  "rate": 2.5,
  "minAmount": 10000,
  "maxAmount": 1000000,
  "maxCashback": 10000,
  "validFrom": "2023-09-01T00:00:00.000Z",
  "validUntil": "2023-12-31T23:59:59.000Z",
  "userTier": ["gold", "platinum"],
  "usageLimit": 5
}
\`\`\`

**Validation Rules:**
- `name`: Required, string
- `service`: Required, enum: ["crypto", "bills", "giftcard", "transfer", "all"]
- `rate`: Required, number 0-100
- `validUntil`: Required, future date

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Cashback rule created successfully",
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "name": "Crypto Purchase Cashback",
    "service": "crypto",
    "rate": 2.5,
    "isActive": true,
    "createdAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 21.3 Get Cashback Rules 👑

\`\`\`http
GET /api/cashback/admin/rules
\`\`\`

**Query Parameters:**
- `service` (optional): Filter by service
- `isActive` (optional): Filter by active status
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "rules": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "name": "Crypto Purchase Cashback",
        "service": "crypto",
        "rate": 2.5,
        "minAmount": 10000,
        "maxCashback": 10000,
        "isActive": true,
        "validFrom": "2023-09-01T00:00:00.000Z",
        "validUntil": "2023-12-31T23:59:59.000Z",
        "userTier": ["gold", "platinum"],
        "createdBy": {
          "name": "Admin User"
        },
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 3,
      "totalItems": 12
    }
  }
}
\`\`\`

---

## 22. Chat System

### Base Route: `/api/chat`

#### 22.1 Initiate Chat 🔐

\`\`\`http
POST /api/chat/user/initiate
\`\`\`

**Request Body:**
\`\`\`json
{
  "subject": "Need help with crypto withdrawal",
  "category": "crypto",
  "priority": "medium"
}
\`\`\`

**Validation Rules:**
- `subject`: Required, max 200 characters
- `category`: Required, enum: ["general", "account", "crypto", "bills", "giftcard", "technical"]
- `priority`: Optional, enum: ["low", "medium", "high", "urgent"]

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Chat session initiated successfully",
  "data": {
    "chatId": "64f8a1b2c3d4e5f6a7b8c9d0",
    "sessionId": "CHAT_2023_001234",
    "status": "waiting",
    "estimatedWaitTime": "5 minutes",
    "queuePosition": 3,
    "createdAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 22.2 Send Message 🔐

\`\`\`http
POST /api/chat/user/:chatId/message
\`\`\`

**Request Body:**
\`\`\`json
{
  "message": "I've been trying to withdraw Bitcoin but it keeps failing",
  "attachments": ["screenshot.png"]
}
\`\`\`

**Validation Rules:**
- `message`: Required, max 1000 characters
- `attachments`: Optional, max 3 files

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Message sent successfully",
  "data": {
    "messageId": "64f8a1b2c3d4e5f6a7b8c9d1",
    "message": "I've been trying to withdraw Bitcoin but it keeps failing",
    "timestamp": "2023-09-06T10:35:00.000Z",
    "status": "delivered"
  }
}
\`\`\`

#### 22.3 Get Chat History 🔐

\`\`\`http
GET /api/chat/user/history
\`\`\`

**Query Parameters:**
- `status` (optional): Filter by status ["active", "ended", "waiting"]
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "chats": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "sessionId": "CHAT_2023_001234",
        "subject": "Need help with crypto withdrawal",
        "status": "ended",
        "category": "crypto",
        "assignedAgent": {
          "name": "Support Agent",
          "avatar": "https://example.com/avatar.jpg"
        },
        "lastMessage": {
          "message": "Thank you for contacting support. Your issue has been resolved.",
          "timestamp": "2023-09-06T11:15:00.000Z",
          "sender": "agent"
        },
        "duration": "45 minutes",
        "rating": 5,
        "createdAt": "2023-09-06T10:30:00.000Z",
        "endedAt": "2023-09-06T11:15:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 2,
      "totalItems": 8
    }
  }
}
\`\`\`

#### 22.4 End Chat 🔐

\`\`\`http
POST /api/chat/user/:chatId/end
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Chat session ended successfully",
  "data": {
    "chatId": "64f8a1b2c3d4e5f6a7b8c9d0",
    "duration": "45 minutes",
    "endedAt": "2023-09-06T11:15:00.000Z",
    "transcript": "https://example.com/chat-transcript.pdf"
  }
}
\`\`\`

---

## 23. Dashboard

### Base Route: `/api/dashboard`

#### 23.1 Get Dashboard Overview 🔐

\`\`\`http
GET /api/dashboard/overview
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "user": {
      "name": "John Doe",
      "email": "john@example.com",
      "tier": "gold",
      "kycLevel": "level2",
      "joinDate": "2023-01-15T10:30:00.000Z"
    },
    "walletBalance": {
      "naira": 125000,
      "usd": 150.75,
      "totalInNaira": 245000
    },
    "cryptoPortfolio": {
      "totalValue": 85000,
      "holdings": [
        {
          "currency": "BTC",
          "amount": 0.002,
          "value": 45000,
          "change24h": 2.5
        },
        {
          "currency": "ETH",
          "amount": 0.025,
          "value": 40000,
          "change24h": -1.2
        }
      ]
    },
    "recentTransactions": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "type": "crypto_buy",
        "amount": 45000,
        "currency": "BTC",
        "status": "completed",
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "quickStats": {
      "totalTransactions": 156,
      "successfulTransactions": 148,
      "totalSpent": 2500000,
      "totalEarned": 45000,
      "referralEarnings": 12000,
      "cashbackEarned": 8500
    },
    "notifications": {
      "unread": 3,
      "recent": [
        {
          "title": "Transaction Completed",
          "message": "Your Bitcoin purchase was successful",
          "type": "success",
          "createdAt": "2023-09-06T10:30:00.000Z"
        }
      ]
    }
  }
}
\`\`\`

#### 23.2 Get Filtered Transactions 🔐

\`\`\`http
GET /api/dashboard/transactions
\`\`\`

**Query Parameters:**
- `type` (optional): Filter by type ["crypto", "bills", "giftcard", "transfer", "funding"]
- `status` (optional): Filter by status ["pending", "completed", "failed", "cancelled"]
- `startDate` (optional): Start date
- `endDate` (optional): End date
- `minAmount` (optional): Minimum amount
- `maxAmount` (optional): Maximum amount
- `page` (optional): Page number
- `limit` (optional): Items per page
- `sortBy` (optional): Sort field ["createdAt", "amount", "status"]
- `sortOrder` (optional): Sort order ["asc", "desc"]

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "reference": "TXN_2023_001234",
        "type": "crypto_buy",
        "service": "crypto",
        "amount": 45000,
        "currency": "BTC",
        "quantity": 0.001,
        "status": "completed",
        "fee": 450,
        "rate": 45000000,
        "description": "Bitcoin purchase",
        "createdAt": "2023-09-06T10:30:00.000Z",
        "completedAt": "2023-09-06T10:32:00.000Z"
      }
    ],
    "summary": {
      "totalTransactions": 156,
      "totalAmount": 2500000,
      "totalFees": 25000,
      "successRate": 94.9
    },
    "pagination": {
      "currentPage": 1,
      "totalPages": 16,
      "totalItems": 156
    }
  }
}
\`\`\`

#### 23.3 Export Transactions PDF 🔐

\`\`\`http
GET /api/dashboard/transactions/export/pdf
\`\`\`

**Query Parameters:**
- Same as filtered transactions endpoint

**Response:**
- Returns PDF file download with transaction history

#### 23.4 Export Transactions CSV 🔐

\`\`\`http
GET /api/dashboard/transactions/export/csv
\`\`\`

**Query Parameters:**
- Same as filtered transactions endpoint

**Response:**
- Returns CSV file download with transaction history

#### 23.5 Get Transaction Summary 🔐

\`\`\`http
GET /api/dashboard/transactions/summary
\`\`\`

**Query Parameters:**
- `period` (optional): Time period ["7d", "30d", "90d", "1y"] (default: "30d")

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "period": "30d",
    "summary": {
      "totalTransactions": 45,
      "totalAmount": 850000,
      "totalFees": 8500,
      "averageTransaction": 18888,
      "successRate": 95.6
    },
    "breakdown": {
      "crypto": {
        "count": 20,
        "amount": 450000,
        "percentage": 52.9
      },
      "bills": {
        "count": 15,
        "amount": 250000,
        "percentage": 29.4
      },
      "giftcard": {
        "count": 10,
        "amount": 150000,
        "percentage": 17.6
      }
    },
    "dailyStats": [
      {
        "date": "2023-09-01",
        "transactions": 3,
        "amount": 45000
      }
    ]
  }
}
\`\`\`

#### 23.6 Update Dashboard Preferences 🔐

\`\`\`http
PUT /api/dashboard/preferences
\`\`\`

**Request Body:**
\`\`\`json
{
  "theme": "dark",
  "currency": "NGN",
  "notifications": {
    "email": true,
    "push": true,
    "sms": false
  },
  "dashboard": {
    "showBalance": true,
    "showPortfolio": true,
    "defaultView": "overview"
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Dashboard preferences updated successfully",
  "data": {
    "theme": "dark",
    "currency": "NGN",
    "updatedAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

---

## 24. Settings

### Base Route: `/api/settings`

#### 24.1 Get User Settings 🔐

\`\`\`http
GET /api/settings
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "profile": {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john@example.com",
      "phone": "+2348012345678",
      "dateOfBirth": "1990-05-15",
      "avatar": "https://example.com/avatar.jpg"
    },
    "security": {
      "twoFactorEnabled": true,
      "biometricEnabled": false,
      "transactionPinSet": true,
      "lastPasswordChange": "2023-08-15T10:30:00.000Z"
    },
    "preferences": {
      "theme": "light",
      "language": "en",
      "currency": "NGN",
      "timezone": "Africa/Lagos"
    },
    "notifications": {
      "email": {
        "transactions": true,
        "security": true,
        "marketing": false,
        "updates": true
      },
      "push": {
        "transactions": true,
        "security": true,
        "marketing": false
      },
      "sms": {
        "transactions": true,
        "security": true
      }
    },
    "privacy": {
      "profileVisibility": "private",
      "activityTracking": true,
      "dataSharing": false
    }
  }
}
\`\`\`

#### 24.2 Update Profile 🔐

\`\`\`http
PUT /api/settings/profile
\`\`\`

**Request Body:**
\`\`\`json
{
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+2348012345678",
  "dateOfBirth": "1990-05-15"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Profile updated successfully",
  "data": {
    "firstName": "John",
    "lastName": "Doe",
    "updatedAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 24.3 Change Password 🔐

\`\`\`http
PUT /api/settings/password
\`\`\`

**Request Body:**
\`\`\`json
{
  "currentPassword": "oldPassword123",
  "newPassword": "newPassword456",
  "confirmPassword": "newPassword456"
}
\`\`\`

**Validation Rules:**
- `currentPassword`: Required
- `newPassword`: Required, min 8 characters, must contain uppercase, lowercase, number, special character
- `confirmPassword`: Required, must match newPassword

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Password changed successfully"
}
\`\`\`

#### 24.4 Get Bank Accounts 🔐

\`\`\`http
GET /api/settings/bank-accounts
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": [
    {
      "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
      "accountNumber": "9015400147",
      "bankCode": "044",
      "bankName": "Access Bank",
      "accountName": "John Doe",
      "isPrimary": true,
      "isVerified": true,
      "createdAt": "2023-09-06T10:30:00.000Z"
    }
  ]
}
\`\`\`

#### 24.5 Add Bank Account 🔐

\`\`\`http
POST /api/settings/bank-accounts
\`\`\`

**Request Body:**
\`\`\`json
{
  "accountNumber": "9015400147",
  "bankCode": "044",
  "accountName": "John Doe"
}
\`\`\`

**Validation Rules:**
- `accountNumber`: Required, 10 digits
- `bankCode`: Required, valid bank code
- `accountName`: Required, must match bank records

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Bank account added successfully",
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "accountNumber": "9015400147",
    "bankName": "Access Bank",
    "accountName": "John Doe",
    "isPrimary": false,
    "isVerified": true
  }
}
\`\`\`

#### 24.6 Delete Bank Account 🔐

\`\`\`http
DELETE /api/settings/bank-accounts/:accountId
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Bank account deleted successfully"
}
\`\`\`

#### 24.7 Update Bank Account 🔐

\`\`\`http
PUT /api/settings/bank-accounts/:accountId
\`\`\`

**Request**
\`\`\`json
{
  "isPrimary": true
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Bank account updated successfully",
  "data": {
    "_id": "64a1b2c3d4e5f6789012345",
    "accountNumber": "9015400147",
    "bankCode": "044",
    "accountName": "John Doe",
    "isPrimary": true,
    "isVerified": true,
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T11:45:00.000Z"
  }
}
\`\`\`

#### 24.8 Update Notification Settings 🔐

\`\`\`http
PUT /api/settings/notifications
\`\`\`

**Request Body:**
\`\`\`json
{
  "email": {
    "transactions": true,
    "security": true,
    "marketing": false,
    "updates": true
  },
  "push": {
    "transactions": true,
    "security": true,
    "marketing": false
  },
  "sms": {
    "transactions": true,
    "security": true
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Notification settings updated successfully"
}
\`\`\`

---

## 25. Disputes

### Base Route: `/api/dispute`

#### 25.1 Create Dispute 🔐

\`\`\`http
POST /api/dispute
\`\`\`

**Request Body:**
\`\`\`json
{
  "transactionId": "64f8a1b2c3d4e5f6a7b8c9d0",
  "type": "failed_transaction",
  "subject": "Bitcoin withdrawal failed but amount was debited",
  "description": "I initiated a Bitcoin withdrawal of 0.001 BTC but the transaction failed. However, the equivalent amount in Naira was debited from my wallet...",
  "amount": 45000,
  "evidence": [
    {
      "type": "screenshot",
      "url": "https://example.com/evidence1.jpg",
      "filename": "transaction_screenshot.jpg"
    }
  ]
}
\`\`\`

**Validation Rules:**
- `transactionId`: Required, valid ObjectId
- `type`: Required, enum: ["failed_transaction", "wrong_amount", "unauthorized_charge", "service_not_delivered", "other"]
- `subject`: Required, max 200 characters
- `description`: Required, max 2000 characters
- `amount`: Required, number > 0

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Dispute created successfully",
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "disputeNumber": "DSP-2023-001234",
    "transactionId": "64f8a1b2c3d4e5f6a7b8c9d0",
    "type": "failed_transaction",
    "status": "pending",
    "priority": "medium",
    "subject": "Bitcoin withdrawal failed but amount was debited",
    "amount": 45000,
    "createdAt": "2023-09-06T10:30:00.000Z",
    "estimatedResolution": "2023-09-08T10:30:00.000Z"
  }
}
\`\`\`

#### 25.2 Get User Disputes 🔐

\`\`\`http
GET /api/dispute
\`\`\`

**Query Parameters:**
- `status` (optional): Filter by status ["pending", "investigating", "resolved", "rejected", "escalated"]
- `type` (optional): Filter by type
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "disputes": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "disputeNumber": "DSP-2023-001234",
        "transactionId": "64f8a1b2c3d4e5f6a7b8c9d0",
        "type": "failed_transaction",
        "status": "investigating",
        "priority": "medium",
        "subject": "Bitcoin withdrawal failed but amount was debited",
        "amount": 45000,
        "ageInDays": 2,
        "lastUpdate": "2023-09-07T14:30:00.000Z",
        "assignedTo": {
          "name": "Support Manager",
          "department": "Disputes"
        },
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "summary": {
      "total": 5,
      "pending": 2,
      "investigating": 1,
      "resolved": 2,
      "rejected": 0
    },
    "pagination": {
      "currentPage": 1,
      "totalPages": 1,
      "totalItems": 5
    }
  }
}
\`\`\`

#### 25.3 Get Dispute Details 🔐

\`\`\`http
GET /api/dispute/:id
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "disputeNumber": "DSP-2023-001234",
    "transactionId": "64f8a1b2c3d4e5f6a7b8c9d0",
    "type": "failed_transaction",
    "status": "investigating",
    "priority": "medium",
    "subject": "Bitcoin withdrawal failed but amount was debited",
    "description": "I initiated a Bitcoin withdrawal of 0.001 BTC but the transaction failed...",
    "amount": 45000,
    "evidence": [
      {
        "type": "screenshot",
        "url": "https://example.com/evidence1.jpg",
        "filename": "transaction_screenshot.jpg",
        "uploadedAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "timeline": [
      {
        "action": "created",
        "description": "Dispute created by user",
        "timestamp": "2023-09-06T10:30:00.000Z"
      },
      {
        "action": "assigned",
        "description": "Assigned to Support Manager",
        "timestamp": "2023-09-06T11:00:00.000Z"
      },
      {
        "action": "investigating",
        "description": "Investigation started",
        "timestamp": "2023-09-07T09:00:00.000Z"
      }
    ],
    "adminNotes": [
      {
        "adminId": "64f8a1b2c3d4e5f6a7b8c9d1",
        "note": "Checking blockchain records for transaction confirmation",
        "createdAt": "2023-09-07T14:30:00.000Z"
      }
    ],
    "transaction": {
      "reference": "TXN_2023_001234",
      "type": "crypto_sell",
      "amount": 45000,
      "currency": "BTC",
      "status": "failed",
      "createdAt": "2023-09-06T09:30:00.000Z"
    },
    "ageInDays": 2,
    "createdAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 25.4 Get All Disputes (Admin) 👑

\`\`\`http
GET /api/dispute/admin/all
\`\`\`

**Query Parameters:**
- `status` (optional): Filter by status
- `priority` (optional): Filter by priority ["low", "medium", "high", "urgent"]
- `assignedTo` (optional): Filter by assigned admin
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "disputes": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "disputeNumber": "DSP-2023-001234",
        "user": {
          "name": "John Doe",
          "email": "john@example.com",
          "tier": "gold"
        },
        "type": "failed_transaction",
        "status": "investigating",
        "priority": "medium",
        "subject": "Bitcoin withdrawal failed but amount was debited",
        "amount": 45000,
        "ageInDays": 2,
        "assignedTo": {
          "name": "Support Manager"
        },
        "createdAt": "2023-09-06T10:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 15,
      "totalItems": 145
    }
  }
}
\`\`\`

#### 25.5 Update Dispute Status (Admin) 👑

\`\`\`http
PATCH /api/dispute/admin/:id/status
\`\`\`

**Request Body:**
\`\`\`json
{
  "status": "resolved",
  "resolution": {
    "action": "refund",
    "amount": 45000,
    "description": "Full refund processed after confirming failed blockchain transaction"
  },
  "adminNote": "Blockchain records confirmed transaction failure. Processing full refund."
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Dispute updated successfully",
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "status": "resolved",
    "resolution": {
      "action": "refund",
      "amount": 45000,
      "description": "Full refund processed after confirming failed blockchain transaction",
      "resolvedBy": "64f8a1b2c3d4e5f6a7b8c9d1",
      "resolvedAt": "2023-09-08T10:30:00.000Z"
    }
  }
}
\`\`\`

#### 25.6 Get Dispute Statistics (Admin) 👑

\`\`\`http
GET /api/dispute/admin/stats
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "overview": {
      "total": 145,
      "pending": 25,
      "investigating": 18,
      "resolved": 95,
      "rejected": 7,
      "escalated": 0
    },
    "byType": {
      "failed_transaction": 65,
      "wrong_amount": 28,
      "unauthorized_charge": 15,
      "service_not_delivered": 22,
      "other": 15
    },
    "byPriority": {
      "low": 45,
      "medium": 68,
      "high": 25,
      "urgent": 7
    },
    "resolutionStats": {
      "averageResolutionTime": "2.5 days",
      "resolutionRate": 95.2,
      "refundAmount": 2500000,
      "compensationAmount": 450000
    },
    "trends": {
      "thisMonth": 25,
      "lastMonth": 18,
      "growth": 38.9
    }
  }
}
\`\`\`

---

## 26. USD Rates

### Base Route: `/api/usd-rate`

#### 26.1 Get Current USD Rate 🔓

\`\`\`http
GET /api/usd-rate/current
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Current USD rate retrieved successfully",
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "buyRate": 785.50,
    "sellRate": 780.00,
    "spread": 5.50,
    "spreadPercentage": 0.71,
    "isActive": true,
    "effectiveDate": "2023-09-06T08:00:00.000Z",
    "rateAge": "2 hours",
    "notes": "Market rate adjustment",
    "updatedBy": {
      "name": "Rate Manager",
      "role": "Finance Admin"
    },
    "createdAt": "2023-09-06T08:00:00.000Z"
  }
}
\`\`\`

#### 26.2 Convert USD to Naira 🔓

\`\`\`http
GET /api/usd-rate/convert/usd-to-naira?amount=100&rateType=buy
\`\`\`

**Query Parameters:**
- `amount`: Required, number > 0
- `rateType`: Optional, enum: ["buy", "sell"] (default: "buy")

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Conversion completed successfully",
  "data": {
    "usdAmount": 100,
    "nairaAmount": 78550,
    "rateType": "buy",
    "rate": 785.50,
    "timestamp": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 26.3 Convert Naira to USD 🔓

\`\`\`http
GET /api/usd-rate/convert/naira-to-usd?amount=78000&rateType=sell
\`\`\`

**Query Parameters:**
- `amount`: Required, number > 0
- `rateType`: Optional, enum: ["buy", "sell"] (default: "sell")

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Conversion completed successfully",
  "data": {
    "nairaAmount": 78000,
    "usdAmount": 100.00,
    "rateType": "sell",
    "rate": 780.00,
    "timestamp": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 26.4 Get USD Rate History 🔓

\`\`\`http
GET /api/usd-rate/history
\`\`\`

**Query Parameters:**
- `startDate` (optional): Start date (ISO 8601)
- `endDate` (optional): End date (ISO 8601)
- `limit` (optional): Number of records (default: 50, max: 100)
- `page` (optional): Page number

**Response:**
\`\`\`json
{
  "success": true,
  "message": "USD rate history retrieved successfully",
  "data": {
    "rates": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "buyRate": 785.50,
        "sellRate": 780.00,
        "spread": 5.50,
        "spreadPercentage": 0.71,
        "effectiveDate": "2023-09-06T08:00:00.000Z",
        "notes": "Market rate adjustment",
        "updatedBy": {
          "name": "Rate Manager"
        },
        "createdAt": "2023-09-06T08:00:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 25,
      "totalItems": 1250,
      "hasNext": true,
      "hasPrev": false
    }
  }
}
\`\`\`

#### 26.5 Get USD Rate Statistics 🔓

\`\`\`http
GET /api/usd-rate/statistics
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "USD rate statistics retrieved successfully",
  "data": {
    "current": {
      "buyRate": 785.50,
      "sellRate": 780.00,
      "spread": 5.50
    },
    "today": {
      "high": 788.00,
      "low": 782.00,
      "average": 785.25,
      "changes": 3
    },
    "week": {
      "high": 795.00,
      "low": 775.00,
      "average": 784.50,
      "trend": "stable",
      "volatility": 2.5
    },
    "month": {
      "high": 810.00,
      "low": 765.00,
      "average": 787.25,
      "trend": "declining",
      "volatility": 5.8
    },
    "lastUpdate": "2023-09-06T08:00:00.000Z",
    "nextUpdate": "2023-09-06T14:00:00.000Z"
  }
}
\`\`\`

#### 26.6 Update USD Rate (Admin) 👑

\`\`\`http
POST /api/usd-rate/update
\`\`\`

**Request Body:**
\`\`\`json
{
  "buyRate": 790.00,
  "sellRate": 785.00,
  "notes": "Rate adjustment due to market conditions",
  "effectiveDate": "2023-09-06T14:00:00.000Z"
}
\`\`\`

**Validation Rules:**
- `buyRate`: Required, number > 0
- `sellRate`: Required, number > 0
- `notes`: Optional, string
- `effectiveDate`: Optional, future date

**Response:**
\`\`\`json
{
  "success": true,
  "message": "USD rate updated successfully",
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d1",
    "buyRate": 790.00,
    "sellRate": 785.00,
    "spread": 5.00,
    "spreadPercentage": 0.64,
    "isActive": true,
    "effectiveDate": "2023-09-06T14:00:00.000Z",
    "notes": "Rate adjustment due to market conditions",
    "previousRate": {
      "buyRate": 785.50,
      "sellRate": 780.00,
      "updatedAt": "2023-09-06T08:00:00.000Z"
    },
    "createdAt": "2023-09-06T14:00:00.000Z"
  }
}
\`\`\`

---

## 27. Admin Panel

### Base Route: `/api/admin`

#### 27.1 Admin Dashboard Overview 👑

\`\`\`http
GET /api/admin/dashboard
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "overview": {
      "totalUsers": 15420,
      "activeUsers": 8750,
      "newUsersToday": 125,
      "totalTransactions": 245680,
      "transactionsToday": 1250,
      "totalVolume": 15750000000,
      "volumeToday": 125000000
    },
    "revenue": {
      "totalRevenue": 125000000,
      "monthlyRevenue": 15000000,
      "dailyRevenue": 850000,
      "revenueGrowth": 12.5
    },
    "services": {
      "crypto": {
        "transactions": 125000,
        "volume": 8500000000,
        "revenue": 85000000
      },
      "bills": {
        "transactions": 85000,
        "volume": 4200000000,
        "revenue": 25000000
      },
      "giftcard": {
        "transactions": 35680,
        "volume": 2850000000,
        "revenue": 15000000
      }
    },
    "alerts": [
      {
        "type": "high_volume",
        "message": "Crypto trading volume 25% above average",
        "severity": "info",
        "timestamp": "2023-09-06T10:30:00.000Z"
      }
    ]
  }
}
\`\`\`

#### 27.2 Get All Users (Admin) 👑

\`\`\`http
GET /api/admin/users
\`\`\`

**Query Parameters:**
- `status` (optional): Filter by status ["active", "inactive", "suspended", "banned"]
- `kycLevel` (optional): Filter by KYC level ["unverified", "level1", "level2", "level3"]
- `tier` (optional): Filter by tier ["basic", "silver", "gold", "platinum"]
- `search` (optional): Search by name, email, or phone
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "users": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "firstName": "John",
        "lastName": "Doe",
        "email": "john@example.com",
        "phone": "+2348012345678",
        "status": "active",
        "kycLevel": "level2",
        "tier": "gold",
        "walletBalance": 125000,
        "totalTransactions": 156,
        "totalVolume": 2500000,
        "lastLogin": "2023-09-06T09:30:00.000Z",
        "joinDate": "2023-01-15T10:30:00.000Z",
        "riskScore": 15,
        "flagged": false
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 1542,
      "totalItems": 15420
    },
    "summary": {
      "total": 15420,
      "active": 14250,
      "inactive": 850,
      "suspended": 250,
      "banned": 70
    }
  }
}
\`\`\`

#### 27.3 Get User Details (Admin) 👑

\`\`\`http
GET /api/admin/users/:userId
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "user": {
      "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john@example.com",
      "phone": "+2348012345678",
      "status": "active",
      "kycLevel": "level2",
      "tier": "gold",
      "joinDate": "2023-01-15T10:30:00.000Z",
      "lastLogin": "2023-09-06T09:30:00.000Z",
      "riskScore": 15,
      "flagged": false
    },
    "wallets": {
      "naira": 125000,
      "usd": 150.75,
      "crypto": [
        {
          "currency": "BTC",
          "balance": 0.002,
          "value": 45000
        }
      ]
    },
    "statistics": {
      "totalTransactions": 156,
      "successfulTransactions": 148,
      "totalVolume": 2500000,
      "totalFees": 25000,
      "averageTransaction": 16025,
      "referralEarnings": 12000
    },
    "recentActivity": [
      {
        "action": "CRYPTO_BUY",
        "description": "Purchased 0.001 BTC",
        "amount": 45000,
        "timestamp": "2023-09-06T10:30:00.000Z"
      }
    ],
    "kyc": {
      "level": "level2",
      "documents": [
        {
          "type": "national_id",
          "status": "approved",
          "uploadedAt": "2023-01-16T10:30:00.000Z"
        }
      ]
    }
  }
}
\`\`\`

#### 27.4 Update User Status (Admin) 👑

\`\`\`http
PATCH /api/admin/users/:userId/status
\`\`\`

**Request Body:**
\`\`\`json
{
  "status": "suspended",
  "reason": "Suspicious activity detected",
  "duration": "7d"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "User status updated successfully",
  "data": {
    "userId": "64f8a1b2c3d4e5f6a7b8c9d0",
    "status": "suspended",
    "reason": "Suspicious activity detected",
    "updatedBy": "64f8a1b2c3d4e5f6a7b8c9d1",
    "updatedAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 27.5 Get System Statistics (Admin) 👑

\`\`\`http
GET /api/admin/statistics
\`\`\`

**Query Parameters:**
- `period` (optional): Time period ["24h", "7d", "30d", "90d", "1y"]

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "period": "30d",
    "users": {
      "total": 15420,
      "new": 1250,
      "active": 8750,
      "growth": 8.8
    },
    "transactions": {
      "total": 45680,
      "volume": 2850000000,
      "fees": 28500000,
      "successRate": 95.2
    },
    "services": {
      "crypto": {
        "transactions": 20000,
        "volume": 1500000000,
        "growth": 15.2
      },
      "bills": {
        "transactions": 15000,
        "volume": 850000000,
        "growth": 8.5
      },
      "giftcard": {
        "transactions": 10680,
        "volume": 500000000,
        "growth": 12.1
      }
    },
    "revenue": {
      "total": 15000000,
      "breakdown": {
        "crypto": 8500000,
        "bills": 4200000,
        "giftcard": 2300000
      },
      "growth": 12.5
    }
  }
}
\`\`\`

---

## 28. Webhooks

### Base Route: `/api/webhook`

#### 28.1 Paystack Webhook 🔓

\`\`\`http
POST /api/webhook/paystack
\`\`\`

**Headers:**
\`\`\`
x-paystack-signature: <webhook_signature>
Content-Type: application/json
\`\`\`

**Request Body (Charge Success):**
\`\`\`json
{
  "event": "charge.success",
  "data": {
    "id": 302961,
    "domain": "live",
    "status": "success",
    "reference": "qTPrJoy9Bx",
    "amount": 10000,
    "message": null,
    "gateway_response": "Successful",
    "paid_at": "2023-09-06T10:30:00.000Z",
    "created_at": "2023-09-06T10:28:00.000Z",
    "channel": "card",
    "currency": "NGN",
    "ip_address": "192.168.1.1",
    "metadata": {
      "userId": "64f8a1b2c3d4e5f6a7b8c9d0",
      "service": "wallet_funding",
      "custom_fields": []
    },
    "customer": {
      "id": 84312,
      "first_name": "John",
      "last_name": "Doe",
      "email": "john@example.com",
      "phone": "+2348012345678"
    },
    "authorization": {
      "authorization_code": "AUTH_8dfhjjdt",
      "bin": "408408",
      "last4": "4081",
      "exp_month": "12",
      "exp_year": "2025",
      "channel": "card",
      "card_type": "visa DEBIT",
      "bank": "Test Bank",
      "country_code": "NG",
      "brand": "visa"
    }
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true
}
\`\`\`

**Webhook Events Handled:**
- `charge.success` - Payment successful
- `charge.failed` - Payment failed
- `transfer.success` - Transfer successful
- `transfer.failed` - Transfer failed
- `transfer.reversed` - Transfer reversed

#### 28.2 Flutterwave Webhook 🔓

\`\`\`http
POST /api/webhook/flutterwave
\`\`\`

**Headers:**
\`\`\`
verif-hash: <webhook_signature>
Content-Type: application/json
\`\`\`

**Request Body (Payment Completed):**
\`\`\`json
{
  "event": "charge.completed",
  "data": {
    "id": 4556448,
    "tx_ref": "FLW_TXN_001234",
    "flw_ref": "FLW-MOCK-72d0b77a0bb5b862b3c6e8b6b5b6b5b6",
    "device_fingerprint": "69e6b7f0sb72037aa8428b70fbe03986c",
    "amount": 100,
    "currency": "NGN",
    "charged_amount": 100,
    "app_fee": 1.4,
    "merchant_fee": 0,
    "processor_response": "Approved by Financial Institution",
    "auth_model": "PIN",
    "ip": "192.168.1.1",
    "narration": "CARD Transaction ",
    "status": "successful",
    "payment_type": "card",
    "created_at": "2023-09-06T10:30:00.000Z",
    "account_id": 17321,
    "customer": {
      "id": 1355932,
      "name": "John Doe",
      "phone_number": "+2348012345678",
      "email": "john@example.com",
      "created_at": "2023-01-15T10:30:00.000Z"
    },
    "card": {
      "first_6digits": "408408",
      "last_4digits": "4081",
      "issuer": "TEST BANK",
      "country": "NG",
      "type": "DEBIT",
      "expiry": "12/25"
    },
    "meta": {
      "userId": "64f8a1b2c3d4e5f6a7b8c9d0",
      "service": "wallet_funding"
    }
  }
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true
}
\`\`\`

**Webhook Events Handled:**
- `charge.completed` - Payment completed
- `charge.failed` - Payment failed
- `transfer.completed` - Transfer completed
- `transfer.failed` - Transfer failed

---

## 29. Mudhaxk Wallet

### Base Route: `/api/mudhaxk-wallet`

#### 29.1 Get Supported Currencies 🔓

\`\`\`http
GET /api/mudhaxk-wallet/currencies
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "currencies": [
      {
        "code": "BTC",
        "name": "Bitcoin",
        "symbol": "₿",
        "decimals": 8,
        "minWithdrawal": 0.0001,
        "maxWithdrawal": 10,
        "withdrawalFee": 0.0005,
        "network": "Bitcoin",
        "confirmations": 3,
        "isActive": true
      },
      {
        "code": "ETH",
        "name": "Ethereum",
        "symbol": "Ξ",
        "decimals": 18,
        "minWithdrawal": 0.01,
        "maxWithdrawal": 100,
        "withdrawalFee": 0.005,
        "network": "Ethereum",
        "confirmations": 12,
        "isActive": true
      }
    ]
  }
}
\`\`\`

#### 29.2 Validate Address 🔓

\`\`\`http
POST /api/mudhaxk-wallet/validate-address
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "BTC",
  "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa"
}
\`\`\`

**Validation Rules:**
- `currency`: Required, enum: ["BTC", "ETH", "XRP", "BNB", "SOL", "DOGE", "ADA", "TRX"]
- `address`: Required, valid cryptocurrency address

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "isValid": true,
    "currency": "BTC",
    "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
    "addressType": "P2PKH",
    "network": "Bitcoin Mainnet"
  }
}
\`\`\`

#### 29.3 Create Wallet 🔐

\`\`\`http
POST /api/mudhaxk-wallet/create
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "BTC"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Wallet created successfully",
  "data": {
    "walletId": "64f8a1b2c3d4e5f6a7b8c9d0",
    "currency": "BTC",
    "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
    "network": "Bitcoin",
    "balance": 0,
    "createdAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 29.4 Get User Wallets 🔐

\`\`\`http
GET /api/mudhaxk-wallet/wallets
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "wallets": [
      {
        "walletId": "64f8a1b2c3d4e5f6a7b8c9d0",
        "currency": "BTC",
        "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "network": "Bitcoin",
        "balance": 0.00125,
        "balanceUSD": 45.50,
        "balanceNGN": 35750,
        "isActive": true,
        "createdAt": "2023-09-06T10:30:00.000Z",
        "lastSyncAt": "2023-09-06T11:30:00.000Z"
      }
    ],
    "totalBalance": {
      "usd": 125.75,
      "ngn": 98750
    }
  }
}
\`\`\`

#### 29.5 Get Wallet Balance 🔐

\`\`\`http
GET /api/mudhaxk-wallet/balance/:currency
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "currency": "BTC",
    "balance": 0.00125,
    "balanceUSD": 45.50,
    "balanceNGN": 35750,
    "address": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
    "lastSyncAt": "2023-09-06T11:30:00.000Z",
    "pendingBalance": 0,
    "confirmedBalance": 0.00125
  }
}
\`\`\`

#### 29.6 Process Withdrawal 🔐📱

\`\`\`http
POST /api/mudhaxk-wallet/withdraw
\`\`\`

**Request Body:**
\`\`\`json
{
  "currency": "BTC",
  "toAddress": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
  "amount": 0.001,
  "options": {
    "priority": "medium",
    "memo": "Withdrawal to external wallet"
  }
}
\`\`\`

**Validation Rules:**
- `currency`: Required, supported currency
- `toAddress`: Required, valid address for currency
- `amount`: Required, number > minWithdrawal
- `options.priority`: Optional, enum: ["low", "medium", "high"]

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Withdrawal initiated successfully",
  "data": {
    "transactionId": "64f8a1b2c3d4e5f6a7b8c9d0",
    "currency": "BTC",
    "amount": 0.001,
    "fee": 0.0005,
    "netAmount": 0.0005,
    "toAddress": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
    "status": "pending",
    "estimatedConfirmation": "2023-09-06T11:00:00.000Z",
    "txHash": null,
    "createdAt": "2023-09-06T10:30:00.000Z"
  }
}
\`\`\`

#### 29.7 Sync Wallet Balances 🔐

\`\`\`http
POST /api/mudhaxk-wallet/sync
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "message": "Wallet balances synced successfully",
  "data": {
    "syncedWallets": 5,
    "totalBalance": {
      "usd": 125.75,
      "ngn": 98750
    },
    "changes": [
      {
        "currency": "BTC",
        "previousBalance": 0.00120,
        "newBalance": 0.00125,
        "change": 0.00005
      }
    ],
    "syncedAt": "2023-09-06T11:30:00.000Z"
  }
}
\`\`\`

#### 29.8 Get Transaction History 🔐

\`\`\`http
GET /api/mudhaxk-wallet/history/:currency
\`\`\`

**Query Parameters:**
- `type` (optional): Filter by type ["deposit", "withdrawal"]
- `status` (optional): Filter by status ["pending", "confirmed", "failed"]
- `limit` (optional): Number of transactions (max 100)
- `page` (optional): Page number

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
        "currency": "BTC",
        "type": "deposit",
        "amount": 0.00125,
        "fee": 0,
        "netAmount": 0.00125,
        "fromAddress": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
        "toAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
        "txHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2w3x4y5z6",
        "status": "confirmed",
        "confirmations": 6,
        "requiredConfirmations": 3,
        "blockHeight": 805123,
        "createdAt": "2023-09-06T09:30:00.000Z",
        "confirmedAt": "2023-09-06T10:15:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 48
    },
    "summary": {
      "totalDeposits": 0.00500,
      "totalWithdrawals": 0.00375,
      "netBalance": 0.00125,
      "totalFees": 0.00025
    }
  }
}
\`\`\`

#### 29.9 Get Transaction by ID 🔐

\`\`\`http
GET /api/mudhaxk-wallet/id/:transactionId
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "_id": "64f8a1b2c3d4e5f6a7b8c9d0",
    "currency": "BTC",
    "type": "withdrawal",
    "amount": 0.001,
    "fee": 0.0005,
    "netAmount": 0.0005,
    "fromAddress": "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
    "toAddress": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
    "txHash": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2w3x4y5z6",
    "status": "confirmed",
    "confirmations": 6,
    "requiredConfirmations": 3,
    "blockHeight": 805125,
    "priority": "medium",
    "estimatedConfirmation": "2023-09-06T11:00:00.000Z",
    "createdAt": "2023-09-06T10:30:00.000Z",
    "confirmedAt": "2023-09-06T10:45:00.000Z",
    "metadata": {
      "userNote": "Withdrawal to external wallet",
      "ipAddress": "192.168.1.1"
    }
  }
}
\`\`\`

#### 29.10 Get Transaction Statistics 🔐

\`\`\`http
GET /api/mudhaxk-wallet/stats
\`\`\`

**Query Parameters:**
- `days` (optional): Number of days (default: 30, max: 365)
- `currency` (optional): Filter by specific currency

**Response:**
\`\`\`json
{
  "success": true,
  "data": {
    "period": "30 days",
    "summary": {
      "totalTransactions": 125,
      "totalDeposits": 45,
      "totalWithdrawals": 80,
      "totalVolume": {
        "usd": 5250.75,
        "ngn": 4125000
      },
      "totalFees": {
        "usd": 52.50,
        "ngn": 41250
      }
    },
    "byCurrency": [
      {
        "currency": "BTC",
        "transactions": 45,
        "volume": 0.125,
        "volumeUSD": 4500,
        "fees": 0.0025,
        "feesUSD": 90
      }
    ],
    "dailyStats": [
      {
        "date": "2023-09-06",
        "transactions": 8,
        "deposits": 3,
        "withdrawals": 5,
        "volume": 0.025
      }
    ]
  }
}
\`\`\`

---

## Error Responses

All endpoints return consistent error responses:

\`\`\`json
{
  "success": false,
  "message": "Error description",
  "error": {
    "code": "ERROR_CODE",
    "details": "Detailed error information"
  },
  "timestamp": "2023-09-06T10:30:00.000Z"
}
\`\`\`

**Common Error Codes:**
- `VALIDATION_ERROR` - Request validation failed
- `AUTHENTICATION_ERROR` - Invalid or missing authentication
- `AUTHORIZATION_ERROR` - Insufficient permissions
- `NOT_FOUND` - Resource not found
- `RATE_LIMIT_EXCEEDED` - Too many requests
- `INTERNAL_SERVER_ERROR` - Server error
- `SERVICE_UNAVAILABLE` - Service temporarily unavailable

---

## Rate Limiting

All endpoints are subject to rate limiting:

- **Public endpoints**: 100 requests per minute
- **Authenticated endpoints**: 1000 requests per minute
- **Admin endpoints**: 5000 requests per minute

Rate limit headers are included in responses:
- `X-RateLimit-Limit`: Request limit per window
- `X-RateLimit-Remaining`: Remaining requests in current window
- `X-RateLimit-Reset`: Time when rate limit resets
