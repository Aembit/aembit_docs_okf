---
type: explanation
title: "Sign-On Policy overview"
description: "Description of what Sign-On Policies are and how they work"
resource: https://docs.aembit.io/user-guide/administration/sign-on-policy/
tags: [sign-on-policy, administration]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# Sign-On Policy overview

Use the Sign-On Policy page to control how users log in to your Aembit Tenant. The settings in this page allow you to customize the login experience and security level according to the organization’s needs. The Sign-On Policy page offers two key options to enhance security and streamline the authentication process:

## Require Single Sign-On

[Section titled “Require Single Sign-On”](#require-single-sign-on)

The following are requirements for using Single Sign-On (SSO):

Paid feature

This option is available only to tenants with enabled Identity Providers feature.

This option mandates that users authenticate through a Single Sign-On provider. This not only simplifies the login process but also enhances security by centralizing authentication management.

When you turn on the require SSO option, your users with the system Super Admin role can always use the native sign-in option (username and password).

## Require multi-factor authentication for native sign-in

[Section titled “Require multi-factor authentication for native sign-in”](#require-multi-factor-authentication-for-native-sign-in)

This option enforces the use of multi-factor authentication (MFA) for users logging in directly through Aembit’s native sign-in method. When enabled, users must provide an MFA code, as well as their password. This markedly increases security by adding an extra layer of protection against unauthorized access.

Aembit provides users a 24-hour grace period once you require users to authenticate with MFA. The grace period resets for any users that update their accounts (for example: due to a password reset or account unlocking activity). After this period, Aembit locks accounts without MFA configured.

## Required permissions

[Section titled “Required permissions”](#required-permissions)

Access to the policy settings on this page requires the **Sign-On Policy** permission.
