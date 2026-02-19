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

Configure an SSR flow in your Okta org so that users can independently sign up using a custom enrollment form and a sign-up link. This configuration enables you to automate group assignments and manage security by enforcing email verification and authenticator enrollment policies.

## Prepare the user profile

Establish a governance layer by creating a custom user profile and setting attribute permissions to Read-Write. This prerequisite ensures that your attributes are available for data collection when you customize the enrollment form.

Create a custom user profile 
A custom user profile policy is required to enable self-service registration (SSR) for specific apps. This policy provides a dedicated space to define unique registration rules, such as collecting custom attributes, automating group assignments, and requiring email verification. You can do this without changing the global default policy of your org.
To create a custom user profile:

Open the Admin Console for your org.
Go to Directory > Profile Editor.
In the Users tab, click Create Okta User Type.
Enter a Display name for your policy, such as Custom User Profile.
Enter a Variable name for your policy, such as customUserProfile. 
Click Save.
Change attribute permissions
Set user attributes to Read-Write before building your form to ensure they are available for data collection. Elevating these permissions allows the enrollment form to display custom fields as editable inputs for end users during the sign-up process.

Go to Directory > Profile Editor.
On the Users tab, click the name of the profile to edit. 
In the Attributes section, click the information icon next to the attribute that you want to edit.
Change the attribute user permission to Read-Write. This setting is required for the attribute to appear as a form input during the profile enrollment form configuration.
	
	Note: You can only set Okta-sourced attributes to Read-Write. Attributes synced from external sources, such as Active Directory (AD), are restricted. To collect AD data, use an Okta-sourced placeholder attribute and map it to AD later.

Configuration updates
To set up a sign-up form with SSR, perform the following configurations:

Enable self-service registration - Activate user-led account creation within a user profile policy.
Assign apps to the user profile policy - Link your applications to the policy to display the registration link to end users.
Configure a group for new users - Automate user organization by instantly assigning new sign-ups to target groups.
Configure the profile enrollment form - Customize the attributes and data fields users must provide during registration.
Set authenticators as optional - Choose which security factors users can skip or must set up to complete their account.
Enable email verification - Enforce identity confirmation before granting users access to your application.

Enable self-service registration
Create a user profile policy before configuring your app to authorize user-led account creation and define data collection requirements. Enabling SSR activates the sign-up link for end users and establishes a governance layer for verification and enrollment rules.
In the Admin Console, go to Security > User Profile Policies. 
Click Add user profile policy.
Enter a policy name, such as SSR enabled, and then click Save. 
Click the pencil icon next to your new policy.
On the Enrollment tab, in the Profile Enrollment section, click Edit.
Set Self-service registration to Allowed, and then click Save.
Assign apps to the user profile policy
Link your app to the policy in the Apps tab of the user profile policy to display the registration link to end users. Perform these steps after the policy is created to ensure that the sign-up flow is accessible for the correct target audience.
Go to Security > User Profile Policies.
Click the pencil icon next to your new user profile policy.
Click the Apps tab.
Click Add an App to This Policy.
Click Apply next to your app, and then click Close.

Assign a group for new users
This configuration ensures that when a user creates an account, Okta adds them to the correct group and collects all necessary information beyond a basic name and email. 

Go to Security > User Profile Policies.
Click the pencil icon next to your policy.
On the Enrollment tab, in the Profile Enrollment section, click Edit.
In the Add the user to group field, enter the name of the group that you want to add,  for example, Retailers.
Click Save.
Note: The user profile policy only supports adding a user to one group during registration. If your registration flow requires that users hold membership in multiple groups, automate the additional assignments using group rules. See Create group rules.
Configure the profile enrollment form
Customize your profile enrollment form by adding new attributes beyond the default name and email attributes. Users receive prompts for information only if a required value is missing and progressive profiling is enabled.
In the Profile enrollment form section of your policy, click Add form input.
Click Choose an attribute from the Universal Directory, and then select an attribute, such as Mobile phone (mobilePhone).
Note: You can only choose attributes with permissions already set to Read-Write. See the preceding section, Change attribute permissions.
Ensure that Input requirement is set to Required or Optional, and then click Save.
Note: The profile enrollment form allows up to 30 user attributes.
Set authenticators as optional 
After you enable SSR, set authenticators as optional to define which security factors, such as passwords or email, users must use to validate their identity during account creation. 

Go to Security > Authenticators.
To set authenticators as optional, click the Enrollment tab.
Click your policy, and then click Actions > Edit.
In the Edit Policy dialog, set the value for specific authenticators, such as Email or Password to Optional in your enrollment policy. This allows users to choose their preferred factors during the sign-up process.
Click Save.
Related Topics
Configure user profiles
Create a custom user type
Add custom attributes to an Okta user profile
Understand attribute rules for the profile enrollment form
