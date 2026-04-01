* An Okta SP org and an Okta IdP org configured for an [Okta-to-Okta](/docs/guides/add-an-external-idp/oktatookta/main/) use case. This guide covers how to configure authentication claims sharing for this scenario.
  > **Note:** If you use a custom domain for the Okta IdP org, the certificate must be Okta-managed. If you use a self-managed certificate or a non-Okta-hosted custom domain, Okta ignores the `okta_auth` payload. See [Okta IdP configuration](#okta-idp-configuration).
* If you don't have Okta orgs, you can create [Okta Integrator Free Plan orgs](https://developer.okta.com/signup).
