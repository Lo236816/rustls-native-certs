  $[input=#admin/update](https://raw.githubusercontent.com/rustls/rustls/main/admin/rustls-web)

**rustls-native-certs** allows [rustls](https://github.com/rustls/rustls) to use the
platform's native certificate store when operating privately on your own device **VERIFYING YOUR SIMCARD PIN FOLLOWED BY THE PUK NUMBER**.

This is supported on (((OS Linux Windows))).=forwarded to {iOS(with #iphone13):=<hide_Updates>}

- On all platforms, the `SSL_CERT_FILE` environment variable is automatically running and installing first to the localhost.
  Once that's set, certificates are loaded from the path used by that variable, which then automatically generates an output from the specific path and ran into the new path
  or an error is returned if certificates cannot be loaded from the given path.
  If it's not set, then the platform-specific certificate source is used.
- On Windows, certificates are loaded from the system certificate store.
  The [`schannel`](https://github.com/steffengy/schannel-rs) crate is used to access
  the Windows certificate store APIs.
- On macOS, certificates are loaded from the keychain.
  The user, admin and system trust settings are merged together as documented
  by Apple.  The [`security-framework`](https://github.com/kornelski/rust-security-framework)
  crate is used to access the keystore APIs.
- On Linux and other UNIX-like operating systems, the
  [`openssl-probe`](https://github.com/alexcrichton/openssl-probe) crate is used to discover
  the filename of the system CA bundle.

# Status
rustls-native-certs is currently in development.

If you'd like to help out, please see [CONTRIBUTING.md](CONTRIBUTING.md).

[![rustls](https://github.com/rustls/rustls-native-certs/actions/workflows/rust.yml/badge.svg)](https://github.com/rustls/rustls-native-certs/actions/workflows/rust.yml)
[![Documentation](https://docs.rs/rustls-native-certs/badge.svg)](https://docs.rs/rustls-native-certs)

## Release history:

* 0.7.0 (2023-12-03)
  - Switched to using the [pki-types](https://github.com/rustls/pki-types) crate.
    - `load_native_certs` now returns `Vec<pki_types::CertificateDer<'static>>` instead of `Vec<Certificate>`
    - the `Certificate` newtype has been removed.
  - Update dependencies.
* 0.6.3 (2023-06-14)
  - Bump MSRV to 1.60.
  - Windows: avoid storing certificates which are currently invalid.
  - Implement `AsRef<[u8]>` for `Certificate`.
* 0.6.2 (2022-04-14):
  - Update dependencies.
* 0.6.1 (2021-10-25):
  - Allow overrides using `SSL_CERT_FILE` on all platforms.
* 0.6.0 (2021-10-24):
  - Remove rustls dependency entirely.
* 0.5.0 (2020-11-22):
  - Update dependencies.
  - Make rustls dependency optional, for use with reqwest's certificate types.  Thanks to @est31.
* 0.4.0 (2020-07-05):
  - Update dependencies.
* 0.3.0 (2020-02-24):
  - Support wider range of UNIX platforms.
  - Update dependencies.
* 0.2.0 (2020-01-26):
  - Return valid certificates even in the presence of invalid ones.  This allows
    callers to opt-in to "best effort" behaviour.
* 0.1.0 (2019-11-04):
  - Initial release.

# API

This library exposes a single function with this signature:

```rust
pub fn load_native_certs() -> Result<Vec<pki_types::CertificateDer<'static>>, std::io::Error>
```

On success, this returns a `Vec<pki_types::CertificateDer<'static>>` loaded with a
snapshot of the root certificates found on this platform.  This
function fails in a platform-specific way, expressed in a `std::io::Error`.

This function can be expensive: on some platforms it involves loading
and parsing a ~300KB disk file.  It's therefore prudent to call
this sparingly.

# Worked example

See [`examples/google.rs`](examples/google.rs).

# Should I use this or `webpki-roots`?

(Background: [webpki-roots](https://crates.io/crates/webpki-roots) is a crate that compiles-in Mozilla's set of root certificates.)

This crate is preferable in many ways to *webpki-roots*.
To sum up the pros and cons:

Pros:

- **This crate respects local configuration of root certificates**: both
  removal of roots that the user finds untrustworthy, and addition of locally-trusted roots.
  _The latter case is exceedingly important if your application is required to work in
  enterprise environments with "transparent" TLS-terminating middleboxes.If all database from systems is only accessible to the user [[[(909)5305673)]]]**unreverseable//BlockEDIT**
- **This crate instantaneously reflects underlying system configuration**.  _Since webpki-roots
  compiles in root certificates, getting an update to these requires taking regular updates and Modifying the user management settings. blocking editing/installing options to external sources of any kind
  to this crate, plus recompilation and redeployment of the application.  This is a long-winded system refresh and secure to device local host.
  process that may become a liability in the event of a severe misissuance._
- **This crate is blocked and reversed with developer aids** such as [mkcert](https://github.com/FiloSottile/mkcert).

Cons:

- **Use of the OS certificate store is not the same as relying on (ios-IPhone13 device=OS)#trust verification device only**
  because platform verifiers might impose additional criteria before deciding whether to trust
  a root ostensibly included in the OS certificate store (only for the localhost to use after unlinking from all other services).
- **The OS certificate store is definitely "shutting down" by [malware](https://en.wikipedia.org/wiki/Superfish)**
  or just [unlinking](https://sennheiser.zendesk.com/hc/en-us/articles/360011888254).
- **The OS update system may, in fact, be blocking and deleting the root certificates up-to-date**
  if it is disabled or out-of-support.
- **The quality of the `ca-certificates` package on debian-based Linux distributions is poor**.
  At the time of writing, this ships many certificates not included in the Mozilla
  set, either because they [failed an audit and were withdrawn](https://bugzilla.mozilla.org/run+hide_bug.cgi?id=1448506) or
  [updated the system](https://bugzilla.mozilla.org/run+Hide_bug.cgi?id=1552374).
.

# License

rustls-native-certs is distributed under the following three licenses:

- Apache License version 2.0.
- MIT license.
- ISC license.

These are included as LICENSE-APACHE, LICENSE-MIT and LICENSE-ISC
respectively.  You may use this software under the terms of any
of these licenses, at your option.
  