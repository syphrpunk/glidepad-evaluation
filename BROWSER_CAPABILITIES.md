# GlidePad browser capabilities

GlidePad is a general-purpose macOS web browser organized around persistent
workspaces. It uses WebKit to render web content and lets users keep frequently
used destinations available from a slide-over panel.

This page documents GlidePad's browser behavior for users and for Apple's review
of the Web Browser Public Key Credential managed capability.

## Open arbitrary websites

GlidePad is not limited to its suggested app catalog. Users can:

- Press `⌘L` to open the address and search interface.
- Enter any `http` or `https` URL.
- Enter plain text to search the public web.
- Follow links and redirects from the current page.
- Use websites that open authentication or other content in popup windows.
- Keep selected destinations as persistent apps in a workspace.

When a user enters a URL, GlidePad navigates directly to that destination and
renders its web content with `WKWebView`. GlidePad does not substitute an
unrelated destination or replace the requested page with native content.

On a normal launch with no web apps in the active workspace, GlidePad displays
its address/search interface and curated destination list.

## Open links from macOS

GlidePad declares both `http` and `https` URL schemes with the `Viewer` role.
When macOS sends a web URL to GlidePad, the app opens the panel, creates a web
app in the active workspace, and navigates directly to the supplied URL.

For example, after installing and registering GlidePad with Launch Services:

```sh
open -a GlidePad https://example.com
```

The destination opens inside GlidePad rather than being transformed into an app-
specific deep link or redirected to a different service.

## Website sessions

Web apps use WebKit website data stores. By default, apps share a browser session
so a user can sign in once and use that session across related sites. Users can
also create isolated sessions for separate accounts.

Popup windows created by a website remain part of the originating WebKit session.
This allows a website's authentication popup to complete and return control to
the original browsing context without copying cookies between browsers.

## Passkeys and security

Websites may initiate standard WebAuthn registration and assertion requests from
the page displayed in GlidePad. GlidePad delegates authentication to WebKit and
Apple's AuthenticationServices framework.

GlidePad:

- Does not receive, export, or store passkey private key material.
- Does not choose a credential without user authorization.
- Uses the relying-party identifier associated with the requesting web origin.
- Leaves credentials managed by Passwords/iCloud Keychain or the user's selected
  credential provider.
- Requires the system authentication UI, such as Touch ID, when requested by the
  credential provider.

The browser entitlement is necessary because users can navigate to relying
parties that GlidePad does not own. An associated-domains relationship is only
available when an app developer controls the relying party's domain and therefore
cannot authorize browser authentication for services such as Google.

## Reviewer verification

A reviewer can verify the browser behavior with the following procedure:

1. Launch GlidePad with an empty workspace and confirm that the address/search
   interface is presented.
2. Press `⌘L`, enter `https://example.com`, and confirm direct navigation.
3. Press `⌘L`, enter a plain-text query, and confirm that web search results open.
4. Open `https://www.webkit.org` through macOS and select GlidePad as the handler.
5. Confirm that GlidePad renders the requested destination directly.
6. Follow links within the page and confirm normal browser navigation.
7. Open a site that uses a popup authentication flow and confirm that the popup
   remains associated with the originating GlidePad session.
Steps 1–7 are available in the public pre-approval evaluation build. That build
intentionally omits the requested managed entitlement so macOS can launch it for
review. After Apple assigns the capability and a matching profile is embedded:

8. On a site that supports WebAuthn, initiate sign-in with an existing passkey
   and confirm that the system-owned credential UI is presented.
9. Initiate creation of a new passkey and confirm it can be saved through the
   system credential provider.

## Technical verification

A local build can be checked with:

```sh
make browser-evidence APP=GlidePad.app
```

The report verifies the bundle identifier, HTTP/HTTPS URL declarations, URL role,
code signature, signed browser entitlement, and the independently authorized
entitlement in the embedded provisioning profile. An ad-hoc signature can contain
the entitlement text but does not authorize it; the complete audit passes only
after Apple grants the managed capability and a matching profile is embedded.

## Apple documentation

GlidePad follows Apple's published browser criteria:

- [Web Browser Public Key Credential entitlement](https://developer.apple.com/documentation/bundleresources/entitlements/com.apple.developer.web-browser.public-key-credential)
- [Passkey use in web browsers](https://developer.apple.com/documentation/authenticationservices/passkey-use-in-web-browsers)
- [Supporting passkeys](https://developer.apple.com/documentation/authenticationservices/supporting-passkeys)
- [Provisioning with managed capabilities](https://help.apple.com/developer-account/#/dev38c81d4cd)
