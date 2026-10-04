# iOS CIS Hardening (End User Profile), Compliance & App Protection

## Objective

This project hardens a personally owned (BYOD-style) iPhone enrolled through the Company Portal app, using the CIS Apple iOS Benchmark **End User** configuration profile (`.mobileconfig`) imported into Intune.
It adds a matching compliance policy and an App Protection Policy for Outlook and Teams, so corporate data is protected both at the device level and at the app level.
The CIS **Institutional** profile was not used: it targets corporate-owned, supervised devices, which would require Apple Business Manager.

### Skills Learned

- Creating and uploading the Apple MDM Push Certificate (and renewing it with the same Apple ID)
- Importing a CIS `.mobileconfig` profile into Intune as a custom configuration profile
- Choosing between the CIS End User and Institutional profiles based on device ownership
- Compliance policy design feeding Conditional Access
- App Protection Policy (MAM) design for personally owned devices
- Enrolling an iPhone through Company Portal and verifying the result end to end

### Tools Used

- Microsoft Intune / Endpoint Manager admin center
- Apple Push Certificates Portal
- CIS Apple iOS Benchmark (End User `.mobileconfig` profile from CIS WorkBench)
- Company Portal (iOS)
- Outlook / Teams for iOS

## Steps

#### 1. Create the Apple MDM Push Certificate

Before Intune can manage any Apple device, the tenant needs an Apple MDM Push Certificate:

1. In Intune: Devices → Enrollment → Apple → **Apple MDM Push Certificate**, accepted the terms and downloaded the Certificate Signing Request (CSR)
2. Signed in to the [Apple Push Certificates Portal](https://identity.apple.com/pushcert) and uploaded the CSR
3. Downloaded the resulting `.pem` certificate from Apple
4. Back in Intune, entered the same Apple ID used on the portal and uploaded the `.pem` file

The certificate is valid for one year and must be renewed with the **same Apple ID** that created it, so a shared/service Apple ID is better than a personal one.

<img width="800" height="450" alt="image" src="docs/img/01-push-certificate.png" />

*Ref 1: Apple MDM Push Certificate*

#### 2. Import the CIS End User Profile

The CIS Apple iOS Benchmark ships two `.mobileconfig` profiles: **End User** (personally owned devices) and **Institutional** (corporate-owned, supervised devices). Because the iPhone here is enrolled through Company Portal as a personal device, only the End User profile applies.

Imported it under Devices → iOS/iPadOS → Configuration → Create → New policy → Templates → **Custom**, uploading the `.mobileconfig` file as-is.

> **Note:** The End User profile is written for unsupervised devices. If any payload reports an error or conflict on the device, check its status under the profile's device status view.

<img width="800" height="450" alt="image" src="docs/img/02-cis-end-user-profile.png" />

*Ref 2: CIS End User profile imported as a custom profile*

#### 3. Assign the Profile

Assigned the profile to `SG-iOS-BYOD-EndUser`, the group the enrolled iPhone's user belongs to.

<img width="800" height="450" alt="image" src="docs/img/03-profile-assignment.png" />

*Ref 3: Profile assignment*

#### 4. Compliance Policy

| Category | Setting | Value |
|---|---|---|
| Device Health | Jailbroken devices | Block |
| Device Properties | Minimum OS version | iOS 17.0 |
| System Security | Require passcode | Require, min length 6 |
| Noncompliance Actions | Mark noncompliant | Immediately |

<img width="800" height="450" alt="image" src="docs/img/04-compliance-policy.png" />

*Ref 4: Compliance policy*

#### 5. App Protection Policy

| Setting | Value |
|---|---|
| Prevent backup of corporate data | Yes |
| Restrict cut/copy/paste to managed apps | Yes |
| Require PIN for access | Yes, numeric, 6 digits |
| Wipe corporate data on unenroll | Yes |

Applied to Outlook and Teams for `SG-iOS-BYOD-EndUser`.

<img width="800" height="450" alt="image" src="docs/img/05-app-protection.png" />

*Ref 5: App protection policy*

#### 6. Enroll the iPhone via Company Portal

Enrollment steps:

1. Installed **Company Portal** from the App Store
2. Signed in with the work account and chose to enroll the device
3. Allowed the management profile download and installed it in Settings
4. Waited for the CIS End User profile and compliance policy to apply

<img width="800" height="450" alt="image" src="docs/img/06-company-portal-enrollment.png" />

*Ref 6: Company Portal enrollment*

#### 7. Validate End to End

Confirmed the device shows Compliant in Intune, the CIS profile shows as Succeeded, and Outlook/Teams enforce the PIN and block copy-paste of corporate data into personal apps.

<img width="800" height="450" alt="image" src="docs/img/07-validation.png" />

*Ref 7: End-to-end validation*

## About

A personally owned iPhone hardened with the CIS Apple iOS End User profile, with compliance and App Protection layered on top, enrolled through Company Portal with no Apple Business Manager required.
