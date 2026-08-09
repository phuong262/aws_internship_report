---
title : "Authentication Testing"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---

### Test Objective

This test evaluates the registration, email confirmation, sign-in, and sign-out workflows of Smart Docs AI. The system uses Amazon Cognito to manage accounts, enforce the password policy, and authenticate users before granting access to the chat page.

### Test Procedure

First, the user opens the sign-in page and attempts to sign in with incorrect credentials. The system rejects the request and displays an appropriate error message.

Next, the user switches to the registration function. When a password without a special character is entered, the system does not create the account and requests a password that complies with the policy. After a valid password is provided, a six-digit confirmation code is sent to the registered email address.

The user enters the confirmation code to activate the account and then signs in with the newly created credentials. After successful authentication, the system redirects the user to the chat page. Finally, the user signs out and returns to the unauthenticated interface.

### Test Results

| ID | Test case | Expected result | Actual result | Status |
|---|---|---|---|---|
| AUTH-01 | Sign in with an incorrect username or password | Reject the sign-in request and display an error | `Incorrect username or password.` is displayed | Pass |
| AUTH-02 | Register with a password that has no special character | Do not create the account and request a valid character | `Password must have symbol characters` is displayed | Pass |
| AUTH-03 | Register with valid information | Send a confirmation code to the registered email | The email verification interface requests a six-digit code | Pass |
| AUTH-04 | Enter a valid confirmation code | Activate the account successfully | The account is confirmed and can be used to sign in | Pass |
| AUTH-05 | Sign in with a confirmed account | Redirect the user to the main interface | The `/chat` page is displayed correctly | Pass |
| AUTH-06 | Sign out of the system | End the session and return to the unauthenticated state | The user is returned to the sign-in interface | Pass |

### Evaluation

The authentication workflow operated correctly in all tested scenarios. In addition to handling successful sign-in, the system provided clear feedback when the credentials or registration password were invalid. Email confirmation ensured that an account could only be activated by a user with access to the corresponding email address. After signing out, the session ended and the user was required to sign in again to continue using the system.

Passwords, confirmation codes, and email addresses must be hidden when test evidence is included in the report.
