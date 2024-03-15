## Service Integration Guide

This guide describes how to integrate services to the EOSC Core Infrastructure Proxy.

### Requirements

#### Eligibility

Services that are eligible for connecting to the EOSC Core Infrastructure Proxy are:

- EOSC Core Services operated in the context of the EOSC-Future project. 
- EOSC Support Services operated in the context of the EOSC-Future project
- EOSC Support Services operated by the EOSC Secretariat and/or the EOSC Association

#### Technical and Policy
Each service is required to provide the following information:

- Service name (in English and optionally in other languages supported by the entity)
- Service description
- Website URL for information about the service; the content found at the URL SHOULD provide more complete information than what is provided in the description
- Information about the organization (Service Owner) information including:
  - Name of the organisation
  - Display name of the organisation used for user-facing interfaces
  - Website URL for information about the organisation
- Organization contact information of the following types:
  - Technical and/or Helpdesk/Support contact information (for redirecting users)
  - Security/incident response (see also Sirtfi)
  - Administrative (optional)
- Service contact information of the following types: (if different from the organisation’s contact information)
  - Technical and/or Helpdesk/Support contact information (for redirecting users)
  - Security/incident response (see also [Sirtfi security framework](https://refeds.org/sirtfi))
  - Administrative (optional)
- Whether a Logo URL is provided (for showing in catalogues); if provided, logos SHOULD:
  - use a transparent background where appropriate to facilitate the usage of logos within a user interface
  - use PNG, or GIF (less preferred), images
  - use HTTPS URLs in order to avoid mixed-content warnings within browsers
  - have a size smaller than 50000 characters when encoded in base64
- Link to the Privacy Notice
- Link to the Acceptable Use Policy / Terms of Use based on the [WISE Baseline AUP template](https://wise-community.org/wise-baseline-aup/)
- The entity is compliant with the [GEANT Code of Conduct version 1](https://wiki.refeds.org/display/CODE/Data+Protection+Code+of+Conduct+Home) or any other code of conduct compatible with legislation and guidelines on data protection and privacy including GDPR
- Whether the service is compliant with the [REFEDS Research & Scholarship Entity Category](https://refeds.org/category/research-and-scholarship)
- The service adheres to the [EOSC Security Operational Baseline](https://zenodo.org/records/7396725)
- The federated protocol(s) the service is supporting
- Other technical information related to the supported federated protocol(s)

### Service Provider integration workflow

To integrate your Service Provider with the EOSC Core Infrastructure Proxy, you need to submit a ticket to the [EOSC Helpdesk](https://eosc-helpdesk.eosc-portal.eu/) indicating your request including the information described in the [Technical and Policy section](#technical-and-policy). Please mention that the request is addressed to the <strong>"EOSC AAI"</strong> support unit.

The integration follows a two-step process:

1. **Register your Service Provider and test integration with the Demo instance of the EOSC Core Infrastructure Proxy**: The demo (acceptance) instance allows for testing authentication and authorisation through the academic and social Identity Providers connected to the EOSC Core Infrastructure Proxy. Note that while the demo instance has identical functionality to the production instance, no information is shared between the two systems.
1. **Register your Service Provider with the production instance of the EOSC Core Infrastructure Proxy**: The production instance allows access to your service through the academic and social Identity Providers connected to the EOSC Core Infrastructure Proxy. This requires that your service meets all the requirements and that integration has been thoroughly tested during Step 1.

The most important URLs for enabling the integration in each environment are listed in the table below but more information can be found in the protocol-specific sections that follow.

| Environment | SAML | OpenID Connect |
| ------------| -----| ---------------|
| Production  | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/saml/descriptor</li><li>https://aai.eosc-portal.eu/proxy/saml2/idp/metadata.php (deprecated)</li></ul> | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/.well-known/openid-configuration</li><li>https://aai.eosc-portal.eu/oidc/.well-known/openid-configuration (deprecated)</li></ul> |
| Demo        | <ul><li>https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/saml/descriptor</li><li>https://aai-demo.eosc-portal.eu/proxy/saml2/idp/metadata.php (deprecated)</li></ul> | https://aai-demo.eosc-portal.eu/oidc/.well-known/openid-configuration |

#### Required Service Information per Environment

|      Information    |        Demo         |      Production     |
| ------------------- | ------------------- | ------------------- |
| Service name	      | Required            | Required            |
| Service description | Required            | Required            |
| Website URL for information about the service | Optional | Optional |
| Information about the organisation (Name of the organisation, Display name of the organisation, Website URL for information about the organisation) | Required | Required |
| Organisation contact information (Technical and/or Helpdesk/Support, Security/incident response, Administrative) | Required | Required |
| Service contact information of the following types (Technical and/or Helpdesk/Support, Security/incident response, Administrative) | Required | Required |
| Logo URL            | Optional            | Optional            |
| Link to the Privacy Notice | Optional     | Required            |
| Link to the Acceptable Use Policy / Terms of Use | Optional | Required |
| The entity is compliant with the GEANT Code of Conduct version 1 or any other code of conduct compatible with legislation and guidelines on data protection and privacy including GDPR | Optional | Optional |
| Whether the service is compliant with the REFEDS Research & Scholarship Entity Category | Optional | Optional |
| The service adheres to the EOSC Security Baseline | Optional | Required

### SAML Service Provider

Service Providers supporting the SAML protocol MUST comply with the [SAML2int SAML 2.0 Interoperability Deployment Profile](https://kantarainitiative.github.io/SAMLprofiles/saml2int.html).

#### Metadata registration

SAML authentication relies on the use of metadata. Both parties (you as a SP and the EOSC Core Infrastructure Proxy IdP) need to exchange metadata in order to know and trust each other. The metadata include information such as the location of the service endpoints that need to be invoked, as well as the certificates that will be used to sign SAML messages. The format of the exchanged metadata should be based on the XML-based [SAML 2.0 specification](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf). Usually, you will not need to manually create such an XML document, as this is automatically generated by all major SAML 2.0 SP software solutions (e.g. Shibboleth, SimpleSAMLphp, Keycloak). It is important that you serve your metadata over HTTPS using a browser-friendly SSL certificate, i.e. issued by a trusted certificate authority.

To use the EOSC Core Infrastructure Proxy IdP for user login, you need to connect your service as a SAML SP by submitting a ticket to the [EOSC Helpdesk](https://eosc-helpdesk.eosc-portal.eu/) indicating your request as described in the [Service Provider integration workflow](#service-provider-integration-workflow).

Please, include the following information in your ticket:

1. The SAML <strong>metadata URL</strong> of your service
1. The <strong>entityID</strong> of your service

You can retrieve the metadata of the EOSC Core Infrastructure Proxy IdP Proxy on a dedicated URL that depends on the integration environment being used:

|     Environment    |    Metadata URL    |    SAML IdP entityID    |
| ------------------ | ------------------ | ----------------------- |
| Production         | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/saml/descriptor</li><li>https://aai.eosc-portal.eu/proxy/saml2/idp/metadata.php (deprecated)</li></ul> | <ul><li><code>https://aai.eosc-portal.eu/auth/realms/core</code></li><li><code>https://aai.eosc-portal.eu/proxy/saml2/idp/metadata.php</code> (deprecated)</li></ul> |
| Demo               | <ul><li>https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/saml/descriptor</li><li>https://aai-demo.eosc-portal.eu/proxy/saml2/idp/metadata.php (deprecated)</li></ul> | <ul><li><code>https://aai-demo.eosc-portal.eu/auth/realms/core</code></li><li><code>https://aai-demo.eosc-portal.eu/proxy/saml2/idp/metadata.php</code> (deprecated)</li></ul> |

#### Metadata

Service Providers supporting the SAML protocol MUST provide a SAML 2.0 Metadata document representing its entity according to the [SAML2int SAML 2.0 Interoperability Deployment Profile](https://kantarainitiative.github.io/SAMLprofiles/saml2int.html) and the [recommendations for upstream metadata produced by eduGAIN participants](https://wiki.geant.org/display/eduGAIN/Best+Current+Practice).

#### Entity Identifier Format 

For Service Providers supporting the SAML protocol, the entity identifier is the SAML <code>entityID</code> attribute. Values of the <code>entityID</code> attribute registered MUST be an absolute URI using the <code>http</code>, <code>https</code> or <code>urn</code> schemes. 

https-scheme URIs are RECOMMENDED for all entities. 

http-scheme and https-scheme URIs used for entityID values MUST contain a host part whose value is a DNS domain. 

#### Scope Format

Service Providers supporting the SAML protocol MUST NOT validate the scope of scoped attribute values released by the EOSC Core Infrastructure IdP Proxy.

#### Attributes

The EOSC Core Infrastructure Proxy IdP is guaranteed to release a minimal subset of the [REFEDS R&S](https://refeds.org/category/research-and-scholarship) attribute bundle as described in the following table:

| Attribute friendly name | Attribute OID | Attribute description | Example value | Availability |
| ----------------------- | ------------- | --------------------- | --------- | --------- |
| <code>voPersonID</code> | <code>urn:oid:1.3.6.1.4.1.25178.4.1.6</code>	| An identifier for the user which is globally unique and not reassignable. Use voPersonID within your application as the unique-identifier key for the user. | <code>123e4567e89b12d3a456426614174000@example.org</code> | Always |
| <code>eduPersonUniqueId</code> (Deprecated in favour of <code>voPersonID</code>) | <code>urn:oid:1.3.6.1.4.1.5923.1.1.1.13</code> | An identifier for the user which is globally unique and not reassignable. Use <code>voPersonID</code> within your application as the unique-identifier key for the user. | <code>123e4567e89b12d3a456426614174000@example.org</code> | Always |
| <code>mail</code> | <code>urn:oid:0.9.2342.19200300.100.1.3</code> | The user's email address. This may not be unique and is not suitable for use as a primary key. | <code>john.doe@example.org</code> | Always |
| <code>displayName</code> | <code>urn:oid:2.16.840.1.113730.3.1.241</code> | The user's full name, in a displayable form. | <code>John Doe</code> | Always |
| <code>givenName</code> | <code>urn:oid:2.5.4.42</code> | The user's first name| <code>John</code> | Always |
| <code>sn</code> | <code>urn:oid:2.5.4.4</code> | The user's last name | <code>Doe</code> | Always |
| <code>voPersonExternalAffiliation</code> | <code>urn:oid:1.3.6.1.4.1.25178.4.1.11</code> | The user's affiliation(s) within one or more security domains (scopes) | <code>faculty@university.example.org</code> | Optional (Only when released by the user's identity provider) |
| <code>eduPersonScopedAffiliation</code> (Deprecated) | <code>urn:oid:1.3.6.1.4.1.5923.1.1.1.9</code> | The user's affiliation(s) within one or more security domains (scopes) | <code>faculty@university.example.org</code> | Optional (Only when released by the user's identity provider) |
| <code> eduPersonEntitlement</code> | <code>urn:oid:1.3.6.1.4.1.5923.1.1.1.7</code> | The user's entitlements, typically expressed as capabilities on resources or, alternatively, as group membership and/or role information | <ul><li><code>urn:mace:example.org:group:group.example.org:role=member#example.org</code></li><li><code>urn:geant:eosc-portal.eu:res:eosc.example.org</code></li></ul> | Optional (Only when released by the user's identity provider) |

#### References
- [Shibboleth SP Configuration](https://shibboleth.atlassian.net/wiki/spaces/SP3/overview)
- [SimpleSAMLphp Service Provider QuickStart](https://simplesamlphp.org/docs/stable/simplesamlphp-sp.html)
- [Keycloak](https://www.keycloak.org/docs/latest/server_admin/#saml-v2-0-identity-providers) 

### OpenID Connect Service Provider

Service Providers can be integrated with the EOSC Core Infrastructure Proxy using OpenID Connect (OIDC). To allow this, the EOSC Core Infrastructure Proxy provides an OpenID Connect (OAuth2) API. Interconnection with the EOSC Core Infrastructure Proxy OIDC Provider allows users to sign in using any of the supported backend authentication mechanisms, such as institutional IdPs registered with eduGAIN, Research Community AAIs or Social Providers. Once the user has signed in, the EOSC Core Infrastructure Proxy can return OIDC Claims containing information about the authenticated user.

#### Client registration

Before your service can use the EOSC Core Infrastructure Proxy OIDC Provider for user login, you need to register an OIDC client by submitting a ticket to the [EOSC Helpdesk](https://eosc-helpdesk.eosc-portal.eu/) indicating your request as described in the [Service Provider integration workflow](#service-provider-integration-workflow).

Please, include the following information in your ticket:

1. One or more <strong>Redirection URIs</strong> (see [Redirection URIs](#redirection-uris))
1. One or more <strong>Scopes</strong> (see [Claims](#claims))
1. Optionally, one or more <strong>Post Logout Redirect URIs</strong> (see [Logout Endpoint](#logout-endpoint))

As a result of the client registration you will obtain <strong>OAuth 2.0 credentials</strong>, which typically include a client ID and client secret.

#### Metadata

Relying parties supporting OpenID Connect SHOULD retrieve the EOSC Core Infrastructure Proxy configuration based on the Issuer information using the [OpenID Discovery specification](https://openid.net/specs/openid-connect-discovery-1_0.html).

#### Grant types

OpenID Connect relying parties utilising the authorization grant type SHOULD use PKCE ([RFC7636](https://tools.ietf.org/html/rfc7636)) in conjunction with the authorisation server in order to detect and prevent attempts to inject (replay) authorisation codes into the authorisation response. The PKCE challenges must be transaction-specific and securely bound to the user agent in which the transaction was started. OpenID Connect relying parties MAY use the "nonce" parameter of the OpenID Connect authentication request as specified in the [OpenID Connect Core specification](https://openid.net/specs/openid-connect-core-1_0.html) in conjunction with the corresponding ID Token claim for the same purpose. OpenID Connect relying parties SHALL NOT use the implicit grant and any other response type causing the authorization server to issue an access token in the authorization response.

#### Claims

OpenID Connect relying parties MUST support requesting Claims about the End-User and the Authentication event using specific scope values as described in the [OpenID Connect Core specification](https://openid.net/specs/openid-connect-core-1_0.html).

The following scope values can be used to request Claims from the EOSC Core Infrastructure Proxy UserInfo Endpoint:

| Scope name | Claim name | Claim description | Example claim value | Availability |
| ---------- | ---------- | ----------------- | ------------------- | ------------ |
| <code>openid</code> | <code>sub</code> | An identifier for the user which is globally unique and not reassignable. Use <code>voperson_id</code> within your application as the unique-identifier key for the user. | <code>123e4567e89b12d3a456426614174000@example.org</code> | Always |
| <ul><li><code>voperson_id</code></li><li><code>aarc</code></li></ul> | <code>voperson_id</code> | An identifier for the user which is globally unique and not reassignable. Use voperson_id within your application as the unique-identifier key for the user. | <code>123e4567e89b12d3a456426614174000@example.org</code> | Always |
| <ul><li><code>profile</code></li><li><code>aarc</code></li></ul> | <code>name</code> | The user's full name, in a displayable form. | <code>John Doe</code> | Always |
| <ul><li><code>profile</code></li><li><code>aarc</code></li></ul> | <code>given_name</code> | The user's first name | <code>John</code> | Always |
| <ul><li><code>profile</code></li><li><code>aarc</code></li></ul> | <code>family_name</code> | The user's last name | <code>Doe</code> | Always | <ul><li><code>email</code></li><li><code>aarc</code></li></ul> | <code>email</code> | The user's email address. This may not be unique and is not suitable for use as a primary key. | <code>john.doe@example.org</code> | Always |
| <ul><li><code>voperson_external_affiliation</code></li><li><code>aarc</code></li></ul> | <code>voperson_external_affiliation</code> (multivalued) | The user's affiliation(s) within one or more security domains (scopes) | <code>faculty@university.example.org</code> | Optional (Only when released by the user's identity provider) |
| <code>eduperson_scoped_affiliation</code> (Deprecated in favour of <code>voperson_external_affiliation</code>) | <code>eduperson_scoped_affiliation</code> (multivalued - deprecated in favour of <code>voperson_external_affiliation</code>) | The user's affiliation(s) within one or more security domains (scopes) | <code>faculty@university.example.org</code> | Optional (Only when released by the user's identity provider) |
| <code>entitlements</code> | <code>entitlements</code> (multivalued) | The user's entitlements, typically expressed as capabilities on resources or, alternatively, as group membership and/or role information | <ul><li><code>urn:mace:example.org:group:group.example.org:role=member</code></li><li><code>urn:geant:eosc-portal.eu:res:eosc.example.org</code></li></ul> | Optional |
| <code>eduperson_entitlement</code> (deprecated in favour of <code>entitlements</code>) | eduperson_entitlement (multivalued - deprecated in favour of <code>entitlements</code>) | The user's entitlements, typically expressed as capabilities on resources or, alternatively, as group membership and/or role information | <ul><li><code>urn:mace:example.org:group:group.example.org:role=member#example.org</code></li><li><code>urn:geant:eosc-portal.eu:res:eosc.example.org</code></li></ul> | Optional |

#### Redirection URIs

OpenID Connect relying parties MUST pre-register one or more Redirection URI to which authentication responses from the EOSC Core Infrastructure Proxy will be sent. The EOSC Core Infrastructure Proxy utilises exact matching of the redirect URI specified in an authentication request against the pre-registered URIs (see [OAuth 2.0 Security Best Current Practise](https://datatracker.ietf.org/doc/id/draft-ietf-oauth-security-topics.html)), with the matching performed as described in [RFC3986](https://tools.ietf.org/html/rfc3986) (Simple String Comparison). Redirection URIs MUST use the schemata defined in [Section 3.1.2.1 of the OpenID Connect Core specification](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest).

#### Endpoints

The most important OIDC/OAuth2 endpoints are listed below:

| Name | Endpoint (Demo environment) | Endpoint (Production environment) |
| ---- | --------------------------- | --------------------------------- |
| Provider configuration | https://aai-demo.eosc-portal.eu/auth/realms/core/.well-known/openid-configuration | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/.well-known/openid-configuration (Keycloak)</li><li>https://aai.eosc-portal.eu/oidc/.well-known/openid-configuration (MITREid Connect)</li></ul> |
| Issuer | <code>https://aai-demo.eosc-portal.eu/auth/realms/core</code> | <ul><li><code>https://aai.eosc-portal.eu/auth/realms/core</code> (Keycloak)</li><li><code>https://aai.eosc-portal.eu/oidc/</code> (MITREid Connect)</li></ul> |
| Client registration | Submit a ticket in [EOSC Helpdesk](https://eosc-helpdesk.eosc-portal.eu/) | Submit a ticket in [EOSC Helpdesk](https://eosc-helpdesk.eosc-portal.eu/) |
| Authorization	| https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/openid-connect/auth | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/openid-connect/auth (Keycloak)</li><li>https://aai.eosc-portal.eu/oidc/authorize (MITREid Connect)</li></ul> |
| Token | https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/openid-connect/token | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/openid-connect/token (Keycloak)</li><li>https://aai.eosc-portal.eu/oidc/token (MITREid Connect)</li></ul> |
| Device Code | https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/openid-connect/auth/device | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/openid-connect/auth/device (Keycloak)</li><li>https://aai.eosc-portal.eu/oidc/devicecode (MITREid Connect)</li></ul> |
| JSON Web Key(JWK)	| https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/openid-connect/certs | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/openid-connect/certs (Keycloak)</li><li>https://aai.eosc-portal.eu/oidc/jwk (MITREid Connect)</li></ul> |
| UserInfo | https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/openid-connect/userinfo | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/openid-connect/userinfo (Keycloak)</li><li>https://aai.eosc-portal.eu/oidc/userinfo (MITREid Connect)</li></ul> |
| Introspection	| https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/openid-connect/token/introspect | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/openid-connect/token/introspect (Keycloak)</li><li>https://aai.eosc-portal.eu/oidc/introspect (MITREid Connect)</li> |
| Logout | https://aai-demo.eosc-portal.eu/auth/realms/core/protocol/openid-connect/logout | <ul><li>https://aai.eosc-portal.eu/auth/realms/core/protocol/openid-connect/logout (Keycloak)</li><li>https://aai.eosc-portal.eu/oidc/saml/logout (MITREid Connect)</li> |

##### Authorization Endpoint

The Authorization Endpoint performs Authentication of the End-User. This is done by sending the User Agent to the Authorization Server's Authorization Endpoint for Authentication and Authorization, using request parameters defined by OAuth 2.0 and additional parameters and parameter values defined by OpenID Connect.

The request parameters of the Authorization endpoint are:

- <code>client_id</code>: id of the client that ask for authentication to the Authorization Server.
- <code>redirect_uri</code>: URI to which the response will be sent.
- <code>scope</code>: A list of attributes that the application requires.
- <code>state</code>: Opaque value used to maintain state between the request and the callback.
- <code>response_type</code>: value that determines the authorization processing flow to be used.
  - For Authorization Code grant set <code>response_type=code</code>. This way the response will include an authorization code.

##### Token Endpoint

To obtain an Access Token, an ID Token, and optionally a Refresh Token, the Client sends a Token Request to the Token Endpoint.

##### Logout Endpoint

The EOSC Core Infrastructure Proxy supports user logout based on the [OpenID Connect RP-Initiated Logout specification](https://openid.net/specs/openid-connect-rpinitiated-1_0.html).

The Logout endpoint is normally obtained via the <code>end_session_endpoint</code> element of the OP's Configuration page (see [Endpoints table](#endpoints)). Parameters used in the logout request are detailed below:

- <code>id_token_hint</code>: The ID Token previously issued by the EOSC Core Infrastructure Proxy to your Relying Party (RP) and provided to the Logout Endpoint as a hint regarding the End-User's current authenticated session with the Client. It indicates the identity of the End-User that the RP is requesting to log out from the EOSC Core Infrastructure Proxy. If the <code>id_token_hint</code> parameter is omitted, the user may be prompted to confirm the logout.
- <code>client_id</code>: This parameter is used to specify the Client Identifier when <code>post_logout_redirect_uri</code> is specified but <code>id_token_hint</code> is not.
- <code>post_logout_redirect_uri</code>: URI to which the RP is requesting that the End-User's browser be redirected after a logout has been performed. This URI should use the <code>HTTPS</code> scheme and the value must have been previously registered (you can submit a ticket to the [EOSC Helpdesk](https://eosc-helpdesk.eosc-portal.eu/) to register one or more <code>post_logout_redirect_uri</code> values). Note that you need to include either the <code>client_id</code> or <code>id_token_hint</code> parameter in case the <code>post_logout_redirect_uri</code> is included.

You can use either <code>HTTP GET</code> or <code>HTTP POST</code> to send the logout request to the Logout Endpoint.

<details>

<summary>Example logout request</summary>

```
curl -X GET ${LOGOUT_ENDPOINT}?
  id_token_hint=${ID_TOKEN}
```
> 💡 You can find the <code>LOGOUT\_ENDPOINT</code> in the [Endpoints table](#endpoints).

</details>

<details>

<summary>Example logout request with redirection</summary>

```
curl -X GET ${LOGOUT_ENDPOINT}?
  post_logout_redirect_uri=${POST_LOGOUT_REDIRECT_URI}&
  client_id=${CLIENT_ID}
``` 
> 💡 You can find the <code>LOGOUT\_ENDPOINT</code> in the [Endpoints table](#endpoints).

</details>
