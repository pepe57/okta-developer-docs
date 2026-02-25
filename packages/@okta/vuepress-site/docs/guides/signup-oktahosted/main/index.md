---
title: Sign users up with an Okta-hosted sign-up form
excerpt: Sign users up for your app by redirecting them to an Okta-hosted enrollment form
layout: Guides
---

Learn how to implement a Self-Service Registration (SSR) flow on a page using the Okta-hosted Sign-In Widget. Then, learn how to customize an out-of-the-box experience for your app using the Okta Auth JavaScript SDK (Auth JS).

---

#### Learning outcomes

* Understand how to set up a basic Svelte/SvelteKit application.
* Understand how to use Okta Auth JS in a JavaScript web app.
* Understand how Okta app integrations and access policies work.

#### What you need

* Node JS LTS installed
* An IDE of your choice, like JetBrains or VSCode, installed
* [Okta Integrator Free Plan org](https://developer.okta.com/signup)
* Familiarity with the [Okta Auth JavaScript SDK](https://github.com/okta/okta-auth-js/blob/master/docs/idx.md#introduction)

In this guide, we’re going to set up a lightweight Svelte application with basic layout capabilities.

## Okta setup

overview here

### Create the app integration for Svelte

The Okta app integration represents your Svelte app in your Okta org and lets you configure how it interacts with Okta services:

1. In the Admin Console, go to **Applications** > **Applications**.
1. Click **Create App Integration**, and then select **OIDC - OpenID Connect** as the **Sign-in method**.
1. Select **Single-Page Application** as your application type, then click **Next**.
1. Give the app integration a name, for example, **Svelte**.
1. Make sure that **Authorization Code** is selected as the grant type.
1. Add the local development URI of `http://localhost:5000` for the following fields:
    * **Sign-in redirect URIs**
    * **Logout redirect URIs**
    * **Base URIs** in the **Trusted Origins** section.
1. In the **Assignments** section, select **Allow everyone in your organization to access** for controlled access.
1. Clear the **Enable immediate access with Federation Broker Mode** checkbox, and then click **Save**.
1. Make note of your app integration's Client ID. You need this later.

> **Note**: For more detailed information on app integration options, see [Create OpenID Connect app integrations](https://help.okta.com/okta_help.htm?type=oie&id=ext_Apps_App_Integration_Wizard-oidc)

??show you how to edit the redirect URLs for a more seamless out-of-the-box sign-in experience in your app.??

### Configure an access policy

Configure an access policy for your app:

1. Go to **Security** > **API**.
1. Click **default** for the Okta default custom authorization server.
1. Click the **Access Policies** tab, and then click **Add New Access Policy** or **Add Policy** if you haven't added any policies yet.
Next you
Input a name of your choosing. I’d go with “Svelte Policy” as my policy name. 
Add a simple description to the description field
in the “assign to” field, click on “The following clients:” and type in your app name and select it from the list. Mine is “Svelte”, so I’d select that.
Now we have our policy, next we add a simple rule for development purposes. To do that:
Click on “Add Rule”,
Add in a name, I’m using “Default Rule”.
Finally, click “Create rule”.
The default settings in the rule are good enough to go off on.

Lastly, we need to set up the user profile policies to allow Self Service Registration (The user can sign up using the Okta-hosted login widget). To do this, from the Admin Console:
Navigate to Security > User Profile Policies.
Click on “Add user profile Policies” and enter a name of your choosing. Mine is “Self Service Registration”. 
Next, click on the edit icon next to your policy. In the enrollment tab > Profile Enrollment, click on “Edit”.
Make sure the Self-service registration field is “Allowed”, 
Also, make sure that in the email verification field, “Required before access is granted” is set to false. (NB: we’re doing this to enable us to test with fake emails during development; once your app is live, you’d want to turn this back on).
Click “Save” at the bottom. Next, go to the Apps tab, click “Add an App to This Policy”, navigate to your app, then click apply, then close.
With that, we’re done!
Svelte Set Up
Next up, we’re going to set up a basic Auth layout in our Svelte application. Firstly, we need to set up the Svelte project. To do this:
Create a new directory in a place of your choosing, and name it something you like. I’m keeping mine simple - “svelte-app”. Open up the folder in your favourite IDE
Next, run this command in your terminal: `npx sv create .` `npx sv create` creates the Svelte project; the “.” argument lets the create command know it should use the current directory.
You might get a prompt that the directory is not empty, but continue with the setup. (This usually happens because most IDEs create hidden config files once you open a directory with them; it’s not a big deal).
Select “SvelteKit minimal” as your template.
When it prompts you to “Add type checking with TypeScript”, select “Yes, using TypeScript syntax”. In the second-to-last prompt, you can add as many libraries as you want to the project; I’m just going to ignore it.
When the setup script asks what package manager you want to install dependencies with, choose npm. With that, the base Svelte setup is done.
Use Auth JS with Svelte
In the final part of this guide, we're going to use AuthJS with our Svelte application to enable a basic, self-registration flow with Okta. In your IDE, navigate to your terminal application and run 
`npm install @okta/okta-auth-js`.
Once that’s done, navigate to `src/lib` and create an ‘okta’ folder. [NB: In prod, you probably would not follow this folder naming convention; you’d probably do something along the lines of ‘providers/iam/okta’, to allow for the addition of new IAM providers. You’d probably also create a standardized IAMProvider class that any new provider can implement. However, for the sake of this tutorial, we’re keeping it very simple.]
In the okta folder, create a ‘config.ts’ file and an index.ts file. Your config.ts file should look like so:
```ts
export default {
 issuer: `https://${yourOktaDomain}/oauth2/default`, //For example, `"https://example.okta.com/oauth2/default"`
 baseUrl: `https://${yourOktaDomain}`, //For example, `"https://example.okta.com/oauth2/default"`
 clientId: `${yourClientID}`, // for example, `0oa2am3kk1CraJ8xO1d7`
 scopes: ['openid', 'email', 'profile'],
 storage: 'sessionStorage',
 redirectUri: 'http://localhost:5000',
 useInteractionCodeFlow: true,
 requireUserSession: 'true',
 authMethod: 'form',
 startService: false,
 useDynamicForm: false,
 uniq: Date.now() + Math.round(Math.random() * 1000), // to guarantee a unique state
};

```
Notice that the redirectUri property is the same as the sign-in redirect URIs that we configured earlier. In your index.ts, write this:
```ts
import config from "$lib/okta/config";
import OktaAuth from "@okta/okta-auth-js";
import type { AccessToken, IDToken, TokenManagerInterface, UserClaims } from "@okta/okta-auth-js";


const authClient = new OktaAuth({
 url: config.baseUrl,
 issuer: config.issuer,
 clientId: config.clientId,
 redirectUri: config.redirectUri,
 scopes: config.scopes,
} as Record<any, any>);


async function login() {
 return await authClient.token.getWithRedirect({ responseType: [ 'id_token' ] });
}


export async function user(): Promise<null | UserClaims> {
 if (authClient.isLoginRedirect()) {
  const { tokens } = await authClient.token.parseFromUrl();
  authClient.tokenManager.setTokens(tokens);
 }
 const tokenManager: TokenManagerInterface = authClient.tokenManager;
 const accessToken: AccessToken = await tokenManager.get('accessToken') as AccessToken;


 if (!accessToken) {
  return null;
 }
 const idToken: IDToken = await tokenManager.get('idToken') as IDToken;
 return await authClient.token.getUserInfo(accessToken, idToken);
}


export const logout = async () => await authClient.signOut();


export default {
 user, logout, login
}
```
This file gives us a couple of helper functions to help us with our authentication. 
The ‘login()’ function uses AuthJS to redirect us to the Okta-hosted login widget.
The ‘user()’ function first checks if we are in a login redirect page, i.e., the user has logged in, and we’ve been redirected back to the home page. If this is true, it retrieves the auth token and saves it, then returns the user information.
The ‘logout()’ function just logs us out of Okta.
The last three lines export the functions in an easy-to-use object. Now we’re going to use this exported objectto set up a basic registration flow.

In `routes/+layout.svelte`, we’re going to create a basic navbar.
Your script tag should look like this:
```ts
import favicon from '$lib/assets/favicon.svg';
import provider from "$lib/okta";
import { onMount } from "svelte";


onMount(async () => {
 let response = await provider.user();
 if (response) user = response;
})


let user = $state(null);
let { children } = $props();
```
As you can see, the user function does all of the work of checking if the user is authenticated, getting said user info, and then saving it in state. This is done in the `onMount()` callback function; that way, we only try to check for the user after the component is mounted.

 In the HTML section, this is just after the `</svlete:head>` tag type in this:
```svelte
<nav>
 <p class="logo">Svelte</p>
 <ul class="nav">
   <li><a href="/about">About</a></li>
   <li><a href="/something">Something</a></li>
   <li><a href="/something-else">Something Else</a></li>
 </ul>
 <div class="auth">
   {#if user}
     <div class="user">
       <p>Welcome {user.name}</p>
       <button onclick={provider.logout} class="logout-btn btn-2">Logout</button>
     </div>
   {:else}
     <button class="login-btn btn" onclick={provider.login}>Login</button>
   {/if}
 </div>
</nav>
```
This is a simple nav bar that shows the user's name after they have been logged in. Two buttons use the `logout` and `login` functions we created earlier. Finally, create a `<style>` tag and add in some css like so:

```css
* {
 font-family: 'Roboto', sans-serif;
}


nav {
 display: flex;
 justify-content: space-evenly;
 width: 100vw;
 border-bottom: 1px solid #c7b8b8;
}


nav ul {
 display: flex;
 list-style: none;
}




p.logo {
 margin: auto 5px;
 text-transform: uppercase;
 font-style: italic;
 font-size: 2rem;
 font-weight: bold;
 font-family: 'Montserrat', serif;
}


nav ul li {
 margin: 10px;
}


nav ul li a {
 text-decoration: none;
 color: #000;
 text-transform: uppercase;
}


nav ul li a:hover {
 text-decoration: underline;
}


button.btn-2 {


 cursor: pointer;
 outline: 0;
 display: inline-block;
 font-weight: 400;
 line-height: 1.5;
 text-align: center;
 background-color: transparent;
 border: 1px solid transparent;
 padding: 6px 12px;
 font-size: 1rem;
 border-radius: .25rem;
 transition: color .15s ease-in-out, background-color .15s ease-in-out, border-color .15s ease-in-out, box-shadow .15s ease-in-out;
 color: #0d6efd;
 border-color: #0d6efd;
}


button.btn-2:hover {
 color: #fff;
 background-color: #0d6efd;
 border-color: #0d6efd;
}


button.btn {
 cursor: pointer;
 color: #fff;
 background-color: #0d6efd;
 border: 1px solid transparent;
 font-weight: 400;
 line-height: 1.5;
 text-align: center;
 padding: 6px 12px;
 font-size: 16px;
 border-radius: .25rem;
 transition: color .15s ease-in-out, background-color .15s ease-in-out, border-color .15s ease-in-out, box-shadow .15s ease-in-out;
}


button.btn:hover {
 color: #fff;
 background-color: #0b5ed7;
 border-color: #0a58ca;
}


.login-btn {
 margin-top: 10px;
}


button.logout-btn {
 padding: 6px 12px;
 color: #d70b0b;
 border-color: #d70b0b;
 margin: auto 10px;
}


.user {
 display: flex;
 margin-top: 10px;
}
```
Let's run the dev server to see our page: `npm run dev – –port 5000`, keep in mind the two double slashes in the command. Those run our dev server on localhost:5000 like we set up on our Okta org and in `src/lib/okta/config.ts`.
At the end of the day, you should end up with a page that looks something like this: 

If you followed the tutorial judiciously, when you click the login button, you should be redirected to the Okta-hosted login widget where you can log in or register, and once that’s done, you should be redirected back to a page that looks like this:



If you look in the earlier code, we have a line that looks like this `{#if user}....{/if}`. You can use this if you pass in the user info as props to child components, and you can conditionally show the information. You can also use the AuthJS  AuthStateManager to do this by doing something like this in your Svelte component script
```ts
authClient = new OktaAuth(config);


// Subscribe to the authState change event.
authClient.authStateManager.subscribe(function(authState) {
 // Logic based on authState is done here.
 if (!authState.isAuthenticated) {
  // render unauthenticated view
  return;
 }


 // Render authenticated view
});
```
With this, we have created a simple Auth flow using the Okta-hosted sign-in widget and Svelte to allow user login and registration, all powered by Okta.
Till next time!
