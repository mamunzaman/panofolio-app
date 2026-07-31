# 03 — Authentication

## Purpose

This document defines the approved Version 1 authentication and account-access lifecycle for PanoFolio.

## Supported authentication flows

- Register
- Verify Email
- Login
- Logout
- Forgot Password
- Reset Password
- Authenticated session restoration

The exact authentication provider and technical implementation will be selected during backend architecture design.

## Registration

A new user registers with the required account information and accepts applicable policies.

After successful registration:

- The account exists in an unverified state.
- A verification message is sent.
- The user must verify the email address before normal authenticated access is granted.

## Email verification

The verification flow must support:

- Valid verification link
- Expired verification link
- Invalid or previously used link
- Resend verification message
- Clear success and failure states

After verification, the user receives the Visitor role.

## Login

Users log in using approved credentials.

The Login screen must handle:

- Valid credentials
- Invalid credentials
- Unverified email
- Disabled or restricted account
- Rate limiting
- Temporary service error

Error messages must be useful without exposing sensitive account information.

## Forgot and reset password

The Forgot Password flow accepts the registered email address and sends a time-limited reset link when appropriate.

The Reset Password flow must handle:

- Valid reset token
- Expired token
- Invalid or used token
- Password validation failure
- Successful password update

## Session behavior

Authenticated sessions must:

- Restore safely after page refresh when valid
- Expire according to the selected security policy
- Be invalidated on logout
- Redirect users away from protected screens when no longer authenticated
- Preserve intended navigation where safe after successful login

## Role assignment

- Registration does not directly create a Creator.
- Verified users become Visitors.
- Visitor-to-Creator transition requires a Creator Access request and Administrator approval.
- Administrator role assignment is a controlled platform operation.

## Creator access request

A verified Visitor may submit a Creator Access request.

Request states should support at least:

- Not submitted
- Pending
- Approved
- Rejected

After approval, the user becomes a Creator and may create exactly one Portfolio.

## Route protection

### Public routes

Accessible without login, including public portfolio and authentication entry screens.

### Authenticated routes

Require a valid user session.

### Creator routes

Require a valid session and Creator role.

### Administrator routes

Require a valid session and Administrator role.

Route protection must be enforced on both client and server.

## Security requirements

- Passwords must never be stored in plaintext.
- Authentication tokens and secrets must be handled securely.
- Sensitive responses must not reveal whether an unrelated account exists.
- Repeated login, reset, and verification attempts must be rate-limited.
- Security-relevant actions should be logged where appropriate.
- Authorization must be verified for every protected API operation.

## Authentication screen inventory

- AUTH-001 Login
- AUTH-002 Register
- AUTH-003 Verify Email
- AUTH-004 Forgot Password
- AUTH-005 Reset Password

Detailed behavior will be defined in `05_Screen_Specifications.md`.

## Future considerations

Possible future additions include social login, passkeys, multi-factor authentication, organization SSO, and trusted devices. These are not approved Version 1 requirements.
