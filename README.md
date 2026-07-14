# GlidePad for macOS — Apple evaluation build

GlidePad is a general-purpose macOS web browser organized around persistent
workspaces. This public repository provides documentation and a notarized build
for Apple's evaluation of GlidePad's Web Browser Public Key Credential entitlement
request. The application source remains in a separate private repository.

## Download

Download the latest Apple evaluation build:

- [GlidePad-evaluation.dmg](https://github.com/syphrpunk/glidepad-evaluation/releases/download/v1.88-evaluation/GlidePad-evaluation.dmg)
- [SHA-256 checksum](https://github.com/syphrpunk/glidepad-evaluation/releases/download/v1.88-evaluation/GlidePad-evaluation.dmg.sha256)

The build is signed with Developer ID, notarized by Apple, and has a stapled
notarization ticket. It does not require an account or evaluation credentials.

Final SHA-256:

```text
33419462a0a29279813eff19e4ea987c4fbe917f533e2d138ca9a13649b177f3  GlidePad-evaluation.dmg
```

## Evaluation

1. Download and open `GlidePad-evaluation.dmg`.
2. Drag `GlidePad.app` to Applications.
3. Launch GlidePad.
4. Press `⌘L` and enter any HTTP or HTTPS URL, such as
   `https://example.com`.
5. Press `⌘L` and enter plain text to perform a web search.
6. Open an external web link with GlidePad and confirm direct navigation.

See [Browser capabilities and security model](BROWSER_CAPABILITIES.md) for the
complete browser behavior, WebAuthn implementation, passkey privacy model, and
reviewer verification procedure.

## Pre-approval entitlement state

This evaluation build intentionally omits
`com.apple.developer.web-browser.public-key-credential` because Apple has not yet
assigned that managed capability to the developer account. Embedding an
unauthorized restricted entitlement would prevent the evaluation build from
launching.

The underlying implementation supports both WebAuthn assertions
(`navigator.credentials.get()`) and registrations
(`navigator.credentials.create()`), integrates with AuthenticationServices, and
supports platform passkeys and external security keys. After Apple assigns the
managed capability, the same implementation will be signed with a provisioning
profile that authorizes iCloud Keychain passkey access.

## Apple documentation

- [Web Browser Public Key Credential entitlement](https://developer.apple.com/documentation/bundleresources/entitlements/com.apple.developer.web-browser.public-key-credential)
- [Passkey use in web browsers](https://developer.apple.com/documentation/authenticationservices/passkey-use-in-web-browsers)
- [Supporting passkeys](https://developer.apple.com/documentation/authenticationservices/supporting-passkeys)
