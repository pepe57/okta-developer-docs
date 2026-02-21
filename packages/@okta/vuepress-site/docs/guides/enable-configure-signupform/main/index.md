---
title: Enable and configure a sign-up form
excerpt: Enable self-service registration and configure a sign-up form to your requirements
layout: Guides
---

Switch on and define the authentication factors and required user information for self-service registration (SSR) in your app.

---

#### Learning outcomes

* Understand how to enable SSR in a user profile policy.
* Learn how to customize a registration form with Okta-mastered attributes.

* Configure authenticator enrollment for new user registrations.

#### What you need

* An [Okta Integrator Free Plan org](https://developer.okta.com/signup/)
* Super admin privileges to modify user profile policies and Universal Directory
* An existing OIDC app integration in your Okta org.

---

## Overview

Self-service registration (SSR) for your apps is turned off in the default user profile policy of your org. This is because many workforce apps are assigned users centrally and therefore don't require a "Create an account" or "Sign-up" button. Configure an SSR flow in your Okta org so that users can independently sign up using a custom enrollment form and a sign-up link. This configuration enables you to automate group assignments and manage security by enforcing email verification and authenticator enrollment policies.

To enable a sign-up flow for your app and give Okta enough information to power it:

* Create a user profile policy that enables SSR.
* Create a profile enrollment policy that defines the user information they need to supply and how it's stored.
* Create an authenticator enrollment policy the defines the required authenticators for your app.

In this guide, you create a new user profile policy that requires users to sign up with three factors: a username, password, and phone.

> **Note**: This doc assumes that you have already designed your SSR flow and are ready to configure it. See [Understand the default SSR flow and how you can customize it](THOMAS) for a description of how various policies, Universal Directory, and optional scripts combine to influence the SSR flow. For information on how to plan your SSR flow, see [Design your sign-up flows for different types of users](BRENT).

## Enable SSR with a user profile policy

A user profile policy governs the information needed from a user for an app. It's also how you enable SSR for the app. Create the policy, enable SSR, and assign it to your app.

### Enable self-service registration

Create a user profile policy before configuring your app to authorize user-led account creation and define data collection requirements. Enabling SSR activates the sign-up link for end users and establishes a governance layer for verification and enrollment rules.

1. Go to **Security** > **User Profile Policies** in the Admin Console.
1. Click **Add user profile policy**.
1. Enter a policy name, such as **SSR enabled**, and then click **Save**.
1. Click the pencil icon next to your new policy.
1. On the **Enrollment** tab, in the **Profile Enrollment** section, click **Edit**.
1. Set **Self-service registration** to **Allowed**, and then click **Save**.

### Assign apps to the user profile policy

Link your app to the policy to display the registration link to end users. Perform these steps after the policy is created to ensure that the sign-up flow is accessible for the correct target audience.

1. On the User Profile Policies page, click the pencil icon next to your new user profile policy.
1. Click the **Apps** tab and click **Add an App to This Policy**.
1. Click **Apply** next to your app, and then click **Close**.

## Set the information a user supplies

By default, every user profile policy requires a user to have a first name, a last name, and a primary email. They aren't added to any pre-defined user groups when they are created. Edit the profile enrollment form and user group in the policy to configure a sign-up form that's tailored to your app's needs. This gives you control over the security and organization of your user base by configuring automated group assignments, defining optional authenticators, ??and enforcing email verification?? to ensure a trusted and streamlined onboarding process.

In this example, set the policy to add the user to a group called **Retailers**, and add an optional mobile phone number to the profile enrollment form from the preset list of Okta properties.

### Assign a group for new users

This configuration ensures that when a user creates an account, Okta adds them to the correct group and collects all necessary information beyond a basic name and email.

1. On the User Profile Policies page, click the pencil icon next to your policy.
1. On the **Enrollment** tab, in the **Profile Enrollment** section, click **Edit**.
1. In the **Add the user to group** box, enter the name of the group that you want to add, for example, **Retailers**.
1. Click **Save**.

  > **Note**: The user profile policy only supports adding a user to one group during registration. If your registration flow requires that users hold membership in multiple groups, automate the additional assignments using group rules. See [Create group rules](https://help.okta.com/okta_help.htm?id=create-group-rules).


This is where I left off

### Configure the profile enrollment form

Customize your profile enrollment form by adding new attributes beyond the default name and email attributes. Users receive prompts for information only if a required value is missing and progressive profiling is enabled.

1. In the **Profile enrollment form** section of your policy, click **Add form input**.
1. Click **Choose an attribute from the Universal Directory** dropdown list, and then select an attribute, such as **Secondary email (secondEmail)**.

   > **Note**: You can only choose attributes with [permissions already set to read-write](#change-attribute-permissions).

1. Ensure that Input requirement is set to **Required** or **Optional**, and then click **Save**.

   > **Note**: The profile enrollment form allows up to 30 user attributes.

## Prepare the user profile

Establish a governance layer by creating a custom user profile and setting attribute permissions to read-write. This prerequisite ensures that your attributes are available for data collection when you customize the enrollment form.

### Create a custom user profile

This policy provides a dedicated space to define unique registration rules, such as collecting custom attributes, automating group assignments, and requiring email verification. You can do this without changing the global default policy of your org:

1. Go to **Directory** > **Profile Editor** in the Admin Console.
1. On the **Users** tab, click **Create Okta User Type**.
1. Enter a **Display name** and **Variable name** for your policy.

   > **Note**: The variable acts as a unique indentifier that allows you to reference the profile and it's attributes.

1. Click **Save**.

### Change attribute permissions

Set user attributes to read-write before building your form. This ensures that the attributes are available for data collection. Elevating these permissions allows the enrollment form to display custom fields as editable inputs for end users during the sign-up process.

1. On the **Users** tab of the Profile Editor page, click the name of the profile that you just created.
1. In the **Attributes** section, click the information icon next to the attribute that you want to edit.
1. Select **Read-Write** for **User permission**. This setting is required for the attribute to appear as a form input.

   > **Note**: You can only set Okta-sourced attributes to **Read-Write**. The indicator to the left of the attribute name shows where that attribute originates. If the source is listed as Okta, it means the attribute is mastered within the Okta Universal Directory. If it lists another app (like OpenID or Workflows), the attribute is sourced from that external system. Attributes synced from external sources, such as Active Directory (AD), are restricted. To collect AD data, use an Okta-sourced placeholder attribute and map it to AD later.





### Set authenticators as optional

Part of the self-registration process is the user setting up the authenticators (for example, password, email, phone) they'll use to validate their identity during subsequent sign-ins. Create an authenticator enrollment policy to manage how and when your end users enroll authenticators. 



After you enable SSR, set authenticators as optional to define which security factors, such as passwords or email, users must use to validate their identity during account creation.

1. Go to **Security** > **Authenticators** in the Admin Console.
1. To set authenticators as optional, click the **Enrollment** tab.
1. Click your policy, and then click **Actions** > **Edit**.
1. In the Edit Policy dialog, set the value for specific authenticators, such as Email or Password to Optional in your enrollment policy. This allows users to choose their preferred factors during the sign-up process.
1. Click Save.

Related Topics

Configure user profiles
Create a custom user type
Add custom attributes to an Okta user profile
Understand attribute rules for the profile enrollment form
