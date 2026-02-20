---
title: Okta SSO
full_title: Okta Configuration Guide | DeepInfra
description: Find out how to use Okta as a Single-Sign-On for DeepInfra
---

##  Contents

* [Supported features](#supported-features)
* [Configuration steps](#configuration-steps)
* [SP Initiated SSO](#sp-initiated-sso)
* [Notes](#notes)

## Supported Features

* Single Sign-On (OpenID Connect) initiated via Okta
* Single Sign-On (OpenID Connect) initiated via DeepInfra
* Automatically creates user accounts in DeepInfra on first sign in

## Configuration Steps

* Install the DeepInfra application in your Okta instance
* Fill in the configuration options:
    * **Team ID** -- your okta subdomain is a great starting point. If you need
      multiple disjoint teams in the same okta instance a.k.a. multi-tenancy,
      you can use `subdomain-group`, for the **Team ID**. Lowercase only,
      starting with subdomain, dashes for separators.
    * **Use Stage** -- leave this blank
* Assign the users or groups that should be able to log into DeepInfra
* Go to the DeepInfra App (inside Okta) → Sign On tab and take note of the
  **Client ID** and **Client Secret**.
* For the **Issuer** (normally your okta domain): there should be a section
  that has a link titled *OpenID Provider Metadata*. Click this link. In the
  JSON document shown, look for a key titled “issuer” and copy the URL-value
* Send an email to feedback@deepinfra.com that you'd like to setup Okta SSO, including:
  * Team ID
  * Issuer
  * Client ID
  * Client Secret
  * Admin email -- the email address of the user, who will be admin of the team
* After the setup is complete the users can start signing in:
  * via okta (from dashboard)
  * via deepinfra's [sso login](/login_sso), where they need to enter the **Team ID**
* The user whose email matches the **Admin email** specified in the email will
  become team admin on first login

## SP-initiated SSO

The sign-in process is initiated from DeepInfra.

1. From your browser, navigate to the [deepinfra login page](https://deepinfra.com/login).
2. Click on `Corporate SSO` button.
3. Enter your **Team ID** and click `SSO Login`
4. Enter your Okta credentials (your email and password) and click "Sign in with Okta".
   If your credentials are valid, you are redirected to the DeepInfra
   dashboard. From there you can click on `Team` to see yourself and the other
   team members.

## Notes

* admin can change team member roles (currently toggle between member and admin)
* admin has access to billing dashboard
* all team members have acess to the same api-tokens and models
* if you're interested in single-user-experience -- i.e each person having his
  own tokens and models, let us know!
