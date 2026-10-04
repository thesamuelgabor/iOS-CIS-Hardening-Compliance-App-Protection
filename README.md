# iOS CIS Hardening (End User Profile), Compliance & App Protection

## Objective

This project hardens a personally owned (BYOD-style) iPhone enrolled through the Company Portal app, using the CIS Apple iOS Benchmark **End User** configuration profile (`.mobileconfig`) imported into Intune.
It adds a matching compliance policy and an App Protection Policy for Outlook and Teams, so corporate data is protected both at the device level and at the app level.
The CIS **Institutional** profile was not used: it targets corporate-owned, supervised devices, which would require Apple Business Manager.

### Skills Learned

- Creating and uploading the Apple MDM Push Certificate (and renewing it with the same Apple ID)
- Creating an enrollment type profile for device enrollment with Company Portal
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

<img width="575" height="175" alt="image" src="https://github.com/user-attachments/assets/ed8def35-2567-444a-85c9-e6fedbf178b3" />

*Ref 1: Apple MDM Push Certificate*

#### 2. Create the Enrollment Type Profile

Created an enrollment type profile in Intune for **Device enrollment with Company Portal**, so the iPhone enrolls by signing in to the Company Portal app (no Apple Business Manager or Automated Device Enrollment involved), and assigned it to `SG-iOS-BYOD-EndUser`.

<img width="1326" height="239" alt="image" src="https://github.com/user-attachments/assets/c3b4ccc2-ec94-4043-9b20-8af1e52035b7" />

*Ref 2: Enrollment type profile (Device enrollment with Company Portal)*

#### 3. Import the CIS End User Profile

The CIS Apple iOS Benchmark ships two `.mobileconfig` profiles: **End User** (personally owned devices) and **Institutional** (corporate-owned, supervised devices). Because the iPhone here is enrolled through Company Portal as a personal device, only the End User profile applies.

Imported it under Devices → iOS/iPadOS → Configuration → Create → New policy → Templates → **Custom**, uploading the `.mobileconfig` file as-is.

> **Note:** The End User profile is written for unsupervised devices. If any payload reports an error or conflict on the device, check its status under the profile's device status view.

<img width="773" height="638" alt="image" src="https://github.com/user-attachments/assets/0f2b6f0a-1385-4dcf-975f-fac758a86c09" />

*Ref 3: CIS End User profile imported as a custom profile*

#### 4. Assign the Profile

Assigned the profile to `SG-iOS-BYOD-EndUser`, the group the enrolled iPhone's user belongs to.

<img width="846" height="133" alt="image" src="https://github.com/user-attachments/assets/57c70c53-f872-48a4-a750-0f4b389ebfc2" />

*Ref 4: Profile assignment*

#### 5. Compliance Policy

| Category | Setting | Value |
|---|---|---|
| Device Health | Jailbroken devices | Block |
| Device Properties | Minimum OS version | iOS 17.0 |
| System Security | Require passcode | Require, min length 6 |
| Noncompliance Actions | Mark noncompliant | Immediately |

<img width="947" height="78" alt="image" src="https://github.com/user-attachments/assets/614ca4fe-19da-4332-aaa5-c0114bd4ffac" />

*Ref 5: Compliance policy*

#### 6. App Protection Policy

| Setting | Value |
|---|---|
| Prevent backup of corporate data | Yes |
| Restrict cut/copy/paste to managed apps | Yes |
| Require PIN for access | Yes, numeric, 6 digits |
| Wipe corporate data on unenroll | Yes |

Applied to All Microsoft Apps for `SG-iOS-BYOD-EndUser`.

<img width="966" height="91" alt="image" src="https://github.com/user-attachments/assets/06a9f397-640f-4c0a-a033-efb3e0bb9be7" />

*Ref 6: App protection policy*

#### 7. Enroll the iPhone via Company Portal

Enrollment steps:

1. Installed **Company Portal** from the App Store
2. Signed in with the work account and chose to enroll the device (using the enrollment type profile from Step 2)
3. Allowed the management profile download and installed it in Settings
4. Waited for the CIS End User profile and compliance policy to apply

<img width="1170" height="586" alt="ompany-portal-enrollment" src="https://github.com/user-attachments/assets/8ef8707e-7936-45bb-976a-f2bee226b622" />

*Ref 7: Company Portal enrollment*

#### 8. Validate End to End

Confirmed the device shows Compliant in Intune, the CIS profile shows as Succeeded, and M365 appps enforce the PIN and block copy-paste of corporate data into personal apps.

<img width="717" height="36" alt="image" src="https://github.com/user-attachments/assets/d21f4a5b-ddaa-4218-b53e-182897072daf" />

*Ref 8: End-to-end validation*
