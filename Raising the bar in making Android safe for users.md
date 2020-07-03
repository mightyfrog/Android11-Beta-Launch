# Raising the bar in making Android safe for users

<https://www.youtube.com/watch?v=MNkFSCRUk6E>

## Android & Security 0:10

Philosophy & Themes

1. Principle of Least Privilege
2. Isolation & Containment - apps must isolated from each other and be contained
3. Defense in Depth

## A quick lok at the past 1:30

### Evolution of Security Features

- Android P
  - fs-verify for Pixel 3
  - FBE SD card support
  - FBE metadata encryption
  - StrongBox HAL
  - TLS by Default
  - DNS over TLS
  - CFI for Pixel kernels
  - Credential-Encrypted backups
- Android Q
  - Media codecs in separate sandbox
  - BoundsSan/IntSan in media codecs and Bluetooth
  - Shadow call stack
  - Execute only memory
  - Scudo hardened memory allocator
  - TLS 1.3 by default
  - Adiantum light-weight crypto

## Android R: Important Security Features 4:10

- Hardening
- Safe by default
- Enable new use cases

### Building trust through certifications for Android 6:00

- [Common Criteria](https://www.commoncriteriaportal.org/)
  - CC/NIAP MDF PP
    - Completed gaps in Android 9 & 10
    - OEMs can work with Google to streamline future evaluations
- [NIST](https://csrc.nist.gov)
  - FIPS 140-2
    - Completed CAVP for BoringSSL on Android 9 & 10
    - In Process for CMVP on Android 11
- [DISA](https://iase.disa.mil/stigs)
  - DISA STIG
    - Addressed gaps in Android 9 & 10
    - OEMs can streamline their evaluatio by falling under Google's Android STIGs

### SafetyNet Attestation API

Anti-abuse API

- 350M Devices
- 800M API calls/day
- 300B API Calls in 2019

## Jetpack Security Library 8:50

## Identity Credentials in Android 9:50

Rust adoption for system level programming