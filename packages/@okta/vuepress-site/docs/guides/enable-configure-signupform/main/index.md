---
title: Enable and configure a sign-up form
excerpt: Enable self-service registration and configure a sign-up form to your requirements
layout: Guides
---

Switch on and define the authentication factors and required user information for self-service registration (SSR) in your app.

---

#### Learning outcomes

* Understand how to enable SSR in a user profile policy.
* Learn how to customize a registration form with Okta-sourced attributes.
* Configure an authenticator enrollment policy for new user registrations.

#### What you need

* An [Okta Integrator Free Plan org](https://developer.okta.com/signup/)
* An admin role that grants permissions to modify user profile policies and Universal Directory (UD)
* An existing OIDC app integration in your org

---

## Overview

Self-service registration (SSR) for your apps is turned off in the default user profile policy of your org. This is because many workforce apps are assigned to users centrally and don't require a "Create an account" or "Sign-up" button.

Configure an SSR flow in your org so that users can independently sign up using a custom enrollment form and a sign-up link. This configuration enables you to automate group assignments and manage security by enforcing email verification and authenticator enrollment policies.

Complete the following steps to enable a sign-up flow for your app and give Okta enough information to power it:

* Create a user profile policy that enables SSR.
* Define the information that user needs to supply.
* Create an authenticator enrollment policy that defines the required authenticators for your app.

<!-- >> **Note**: This doc assumes that you have already designed your SSR flow and are ready to configure it. See [Plan self-service registration flows](/docs/concepts/self-service-registration/) to learn how various policies, Universal Directory, and optional scripts combine to influence the SSR flow. For information on how to plan your SSR flow, see [Design your sign-up flows for different types of users](BRENT). -->

## Enable SSR with a user profile policy

A user profile policy governs the information that's needed from a user for an app. It's also how you enable SSR for the app. Create the policy, enable SSR, and assign it to your app.

### Create a user profile policy

Create a user profile policy before configuring your app to authorize user-led account creation and define data collection requirements.

1. In the Admin Console, go to **Security** > **User Profile Policies**.
1. Click **Add user profile policy**.
1. Enter a policy name, and then click **Save**.

> **Note**: See [Configure user profile policies](https://help.okta.com/okta_help.htm?type=oie&id=ext-create-profile-enrollment) for more options.

### Enable SSR

Enable SSR to activate the sign-up link for end users and establish a governance layer for verification and enrollment rules.

1. On the **User profile policies** page, click the pencil icon next to your new policy.
1. On the **Enrollment** tab, in the **Profile Enrollment** section, click **Edit**.
1. Set **Self-service registration** to **Allowed**, and then click **Save**.

> **Note**: Email verification is required by default. If you would prefer to not verify the user's email as part of the registration process, clear that option.

### Assign apps to the user profile policy

Link your [OIDC app](#what-you-need) to the policy to display the registration link to end users. Perform these steps after the policy is created to ensure that the sign-up flow is accessible for the correct target audience.

1. On the **User profile policies** page, click the pencil icon next to your new user profile policy.
1. Click the **Apps** tab, and then click **Add an App to This Policy**.
1. Click **Apply** next to your app, and then click **Close**.

## Set the information a user supplies

By default, every user profile policy requires a user to have a first name, a last name, and a primary email. Also, they aren't added to any pre-defined user groups when they’re created.

Edit the profile enrollment form and add a user group in the policy to configure a sign-up form that's tailored to your app's needs. This gives you control over the security and organization of your user base. Configure automated group assignments, define optional authenticators, and enforce email verification to ensure a trusted and streamlined sign-up process.

### Configure a group for new users

This configuration ensures that when a user creates an account, Okta adds them to the correct group.

1. On the **User profile policies** page, click the pencil icon next to your policy.
1. On the **Enrollment** tab, in the **Profile Enrollment** section, click **Edit**.
1. In the **Add the user to group** box, enter the name of the group that you want to add, for example, **Contractors**.
1. Click **Save**.

  > **Note**: The user profile policy only supports adding a user to one group during registration. If your registration flow requires that users hold membership in multiple groups, automate the additional assignments using group rules. See [Create group rules](https://help.okta.com/okta_help.htm?id=create-group-rules).

### Change attribute permissions

The default Okta user profile defines 31 attributes that you can add directly to the profile enrollment form. If you need to collect information beyond the default attributes, you can also add custom attributes to this profile.

Set user attributes to read-write before building your profile enrollment form. This ensures that the attributes are available for data collection. Elevating these permissions allows the enrollment form to display fields as editable inputs for end users during the sign-up process.

1. Go to **Directory** > **Profile Editor**.
1. Select **Okta** from the **Filters** list.
1. Click **User (default)**.
1. In the **Attributes** section, click the information icon next to the attribute that you want to edit.
1. Select **Read-Write** for the **User permission**. This setting is required for the attribute to appear as a form input.

   > **Note**: You can only set Okta-sourced attributes to **Read-Write**. The indicator to the left of the attribute name shows where that attribute originates. If the source is listed as Okta, it means that the attribute is sourced within the Okta Universal Directory. If it lists another app (like OpenID or Workflows), the attribute is sourced from that external system. Attributes synced from external sources, such as Active Directory, are restricted.

   > **Note**: To add custom attributes to the Okta user default profile, see [Add custom attributes to an Okta user profile](https://help.okta.com/okta_help.htm?id=csh-usgp-add-custom-user-attributes).

### Configure the profile enrollment form

To require user information in addition to name and email, add attributes to the profile enrollment form in your user profile policy. Users are prompted for information only if a required value is missing and progressive profiling is enabled (the default value).

1. Go to **Security** > **User Profile Policies** and click the pencil icon next to your policy.
1. In the **Profile enrollment form** section of your policy, click **Add form input**.
1. Click **Choose an attribute from the Universal Directory** dropdown list, and then select an attribute, such as **Mobile phone (mobilePhone)**.

   > **Note**: You can only choose attributes with [permissions already set to read-write](#change-attribute-permissions).

1. Ensure that the **Input requirement** option is set to **Required**, and then click **Save**.

   > **Note**: The profile enrollment form allows up to 30 user attributes.

### Create an authenticator enrollment policy

Part of self-registration is the user setting up authenticators (for example, email or phone) that they use to validate their identity when they sign in. Create an authenticator enrollment policy to manage how and when your end users enroll authenticators when they sign up.

1. In the Admin Console, go to **Security** > **Authenticators**.
1. To add authenticators, click **Add authenticator**. See [Multifactor authentication](https://help.okta.com/okta_help.htm?type=oie&id=ext-about-authenticators) for detailed information on the supported authenticators.
1. To create your authenticator enrollment policy, click the **Enrollment** tab. Then, use [Create an authenticator enrollment policy](https://help.okta.com/okta_help.htm?type=oie&id=ext-create-mfa-policy) to configure your policy.

   > **Note**: When you set the values for specific authenticators, use **Optional** for some to allow users to choose which authenticators to enroll during the sign-up process.

## Related Topics

* [Configure user profile policies](https://help.okta.com/okta_help.htm?type=oie&id=ext-create-profile-enrollment)
* [Add custom attributes to an Okta user profile](https://help.okta.com/okta_help.htm?id=csh-usgp-add-custom-user-attributes)
* [Understand attribute rules for the profile enrollment form](https://help.okta.com/okta_help.htm?type=oie&id=csh-enroll-form-attributes)
