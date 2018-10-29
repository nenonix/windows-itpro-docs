---
title: How Windows Hello for Business works - Technology and Terms
description: Explains registration, authentication, key material, and infrastructure for Windows Hello for Business.
ms.prod: w10
ms.mktglfcycl: deploy
ms.sitesec: library
ms.pagetype: security
author: mikestephens-MS
ms.author: mstephen
localizationpriority: high
ms.date: 08/19/2018
---
# Technology and Terms

**Applies to:**
-   Windows 10

- [Attestation Identity Keys](#Attestation-Identity-Keys)
- [Azure AD Joined](#Azure-AD-Joined)
- [Azure AD Registered](#Azure-AD-Registered)
- [Certificate Trust](#Certificate-Trust)
- [Cloud Deployment](#Cloud-Deployment)
- [Deployment Type](#Deployment-Type)
- [Endorsement Key](#Endorsement-Key)
- [Federated Environment](#Federated-Environment)
- [Hybrid Azure AD Joined](#Hybrid-Azure-AD-Joined)
- [Hybrid Deployment](#Hybrid-Deployment)
- [Join Type](#Join-Type)
- [Key Trust](#Key-Trust)
- [Managed Environment](#Managed-Environment)
- [On-premises Deployment](#Onpremises-Deployment)
- [Pass-through Authentication](#Passthrough-Authentication)
- [Password Hash Synchronization](#Password-Hash-Synchronization)
- [Primary Refresh Token](#Primary-Refresh-Token) 
- [Storage Root Key](#Storage-Root-Key)
- [Trust Type](#Trust-Type)
- [Trusted Platform Module](#Trusted-Platform-Module)
<hr>

## Attestation Identity Keys
Because the endorsement certificate is unique for each device and does not change, the usage of it may present privacy concerns because it's theoretically possible to track a specific device. To avoid this privacy problem, Windows 10 issues a derived attestation anchor based on the endorsement certificate. This intermediate key, which can be attested to an endorsement key, is the Attestation Identity Key (AIK) and the corresponding certificate is called the AIK certificate. This AIK certificate is issued by a Microsoft cloud service.

> [!NOTE]
> The AIK certificate must be provisioned in conjunction with a third-party service like the Microsoft Cloud CA service. After it is provisioned, the AIK private key can be used to report platform configuration. Windows 10 creates a signature over the platform log state (and a monotonic counter value) at each boot by using the AIK.
> The AIK is an asymmetric (public/private) key pair that is used as a substitute for the EK as an identity for the TPM for privacy purposes. The private portion of an AIK is never revealed or used outside the TPM and can only be used inside the TPM for a limited set of operations. Furthermore, it can only be used for signing, and only for limited, TPM-defined operations.

Windows 10 creates AIKs protected by the TPM, if available, that are 2048-bit RSA signing keys. Microsoft hosts a cloud service called Microsoft Cloud CA to establish cryptographically that it is communicating with a real TPM and that the TPM possesses the presented AIK. After the Microsoft 
Cloud CA service has established these facts, it will issue an AIK certificate to the Windows 10 device.

Many existing devices that will upgrade to Windows 10 will not have a TPM, or the TPM will not contain an endorsement certificate. **To accommodate those devices, Windows 10 allows the issuance of AIK certificates without the presence of an endorsement certificate.** Such AIK certificates are not issued by Microsoft Cloud CA. Note that this is not as trustworthy as an endorsement certificate that is burned into the device during manufacturing, but it will provide compatibility for advanced scenarios like Windows Hello for Business without TPM.

In the issued AIK certificate, a special OID is added to attest that endorsement certificate was used during the attestation process. This information can be leveraged by a relying party to decide whether to reject devices that are attested using AIK certificates without an endorsement certificate or accept them. Another scenario can be to not allow access to high-value assets from devices that are attested by an AIK certificate that is not backed by an endorsement certificate.

### Related topics
[Endorsement Key](#Endorsement-Key), [Storage Root Key](#Storage-Root-Key), [Trusted Platform Module](#Trusted-Platform-Module)

### More information
- [Windows Client Certificate Enrollment Protocol: Glossary](https://msdn.microsoft.com/library/cc249746.aspx#gt_70efa425-6b46-462f-911d-d399404529ab)
- [TPM Library Specification](https://trustedcomputinggroup.org/resource/tpm-library-specification/)


[Return to Top](#Technology-and-Terms)
## Azure AD Joined
Azure AD Join is intended for organizations that desire to be cloud-first or cloud-only. There is no restriction on the size or type of organizations that can deploy Azure AD Join. Azure AD Join works well even in an hybrid environment and can enable access to on-premise applications and resources.
### Related topics
[Join Type](#Join-Type), [Hybrid Azure AD Joined](#Hybrid-Azure-AD-Joined)

### More information
 - [Introduction to device management in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-introduction).

[Return to Top](#Technology-and-Terms)
## Azure AD Registered
The goal of Azure AD registered devices is to provide you with support for the Bring Your Own Device (BYOD) scenario. In this scenario, a user can access your organization's Azure Active Directory controlled resources using a personal device. 
### Related topics
[Azure AD Joined](#Azure-AD-Joined), [Hybrid Azure AD Joined](#Hybrid-Azure-AD-Joined), [Join Type](#Join-Type)

### More information
- [Introduction to device management in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-introduction)


[Return to Top](#Technology-and-Terms)
## Certificate Trust
The certificate trust model uses a securely issued certificate based on the user's Windows Hello for Business identity to authenticate to on-premises Active Directory.  The certificate trust model is supported in hybrid and on-premises deployments and is compatible with Windows Server 2008 R2 and later domain controllers.

### Related topics
[Deployment Type](#Deployment-Type), [Hybrid Azure AD Joined](#Hybrid-Azure-AD-Joined), [Hybrid Deployment](#Hybrid-Deployment), [Key Trust](#Key-Trust), [On-premises Deployment](#Onpremises-Deployment), [Trust Type](#Trust-Type)

### More information 
- [Windows Hello for Business Planning Guide](hello-planning-guide.md)

[Return to Top](#Technology-and-Terms)
## Cloud Deployment
The Windows Hello for Business Cloud deployment is exclusively for organizations using cloud-based identities and resources.  Device management is accomplished using Intune or a modern management alternative. Cloud deployments use Azure AD joined or Azure AD registered device join types.

### Related topics
[Azure AD Joined](#Azure-AD-Joined), [Azure AD Registered](#Azure-AD-Registered), [Deployment Type](#Deployment-Type), [Join Type](#Join-Type)

[Return to Top](#Technology-and-Terms)
## Deployment Type
Windows Hello for Business has three deployment models to accommodate the needs of different organizations.  The three deployment models include:
- Cloud
- Hybrid
- On-Premises

### Related topics
[Cloud Deployment](#Cloud-Deployment), [Hybrid Deployment](#Hybrid-Deployment), [On-premises Deployment](#Onpremises-Deployment)

### More information
- [Windows Hello for Business Planning Guide](hello-planning-guide.md)

[Return to Top](#Technology-and-Terms)
## Endorsement Key

The TPM has an embedded unique cryptographic key called the endorsement key. The TPM endorsement key is a pair of asymmetric keys (RSA size 2048 bits).

The endorsement key public key is generally used for sending securely sensitive parameters, such as when taking possession of the TPM that contains the defining hash of the owner password. The EK private key is used when creating secondary keys like AIKs.

The endorsement key acts as an identity card for the TPM.

The endorsement key is often accompanied by one or two digital certificates:

-   One certificate is produced by the TPM manufacturer and is called the **endorsement certificate**. The endorsement certificate is used to prove the authenticity of the TPM (for example, that it's a real TPM manufactured by a specific chip maker) to local processes, applications, or cloud services. The endorsement certificate is created during manufacturing or the first time the TPM is initialized by communicating with an online service.
-   The other certificate is produced by the platform builder and is called the **platform certificate** to indicate that a specific TPM is integrated with a certain device.
For certain devices that use firmware-based TPM produced by Intel or Qualcomm, the endorsement certificate is created when the TPM is initialized during the OOBE of Windows 10.

### Related topics
[Attestation Identity Keys](#Attestation-Identity-Keys), [Storage Root Key](#Storage-Root-Key), [Trusted Platform Module](#Trusted-Platform-Module)

### More information
- [Understand the TPM endorsement key](https://go.microsoft.com/fwlink/p/?LinkId=733952).
- [TPM Library Specification](https://trustedcomputinggroup.org/resource/tpm-library-specification/)

[Return to Top](#Technology-and-Terms)
## Federated Environment
Primarily for large enterprise organizations with more complex authentication requirements, on-premises directory objects are synchronized with Azure Active Directory and users accounts are managed on-premises. With AD FS, users have the same password on-premises and in the cloud and they do not have to sign in again to use Office 365 or other Azure-based applications. This federated authentication model can provide additional authentication requirements, such as smart card-based authentication or a third-party multi-factor authentication and is typically required when organizations have an authentication requirement not natively supported by Azure AD.

### Related topics
[Hybrid Deployment](#Hybrid-Deployment), [Managed Environment](#Managed-Environment), [Pass-through authentication](#Passthrough-authentication), [Password Hash Sync](#Password-Hash-Sync)

### More information
- [Choosing the right authentication method for your Azure Active Directory hybrid identity solution](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)

[Return to Top](#Technology-and-Terms)
## Hybrid Azure AD Joined
For more than a decade, many organizations have used the domain join to their on-premises Active Directory to enable:
- IT departments to manage work-owned devices from a central location.
- Users to sign in to their devices with their Active Directory work or school accounts. 
Typically, organizations with an on-premises footprint rely on imaging methods to provision devices, and they often use System Center Configuration Manager (SCCM) or group policy (GP) to manage them.
If your environment has an on-premises AD footprint and you also want benefit from the capabilities provided by Azure Active Directory, you can implement hybrid Azure AD joined devices. These are devices that are both, joined to your on-premises Active Directory and your Azure Active Directory.

### Related topics
[Azure AD Joined](#Azure-AD-Joined), [Azure AD Registered](#Azure-AD-Registered), [Hybrid Deployment](#Hybrid-Deployment)

### More information
- [Introduction to device management in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-introduction)

[Return to Top](#Technology-and-Terms)
## Hybrid Deployment
The Windows Hello for Business hybrid deployment is for organizations that have both on-premises and cloud resources that are accessed using a managed or federated identity that is synchronized with Azure Active Directory.  Hybrid deployments support devices that are Azure AD registered, Azure AD joined, and hybrid Azure AD joined.  The Hybrid deployment model supports two trust types for on-premises authentication, key trust and certificate trust.

### Related topics
[Azure AD Joined](#Azure-AD-Joined), [Azure AD Registered](#Azure-AD-Registered), [Hybrid Azure AD Joined](#Hybrid-Azure-AD-Joined), 

### More information
- [Windows Hello for Business Planning Guide](hello-planning-guide.md)

[Return to Top](#Technology-and-Terms)
## Join type
Join type is how devices are associated with Azure Active Directory. For a device to authenticate to Azure Active Directory it must be registered or joined.
Registering a device to Azure AD enables you to manage a device's identity. When a device is registered, Azure AD device registration provides the device with an identity that is used to authenticate the device when a user signs-in to Azure AD. You can use the identity to enable or disable a device.
When combined with a mobile device management(MDM) solution such as Microsoft Intune, the device attributes in Azure AD are updated with additional information about the device. This allows you to create conditional access rules that enforce access from devices to meet your standards for security and compliance. For more information on enrolling devices in Microsoft Intune, see Enroll devices for management in Intune .
Joining a device is an extension to registering a device. This means, it provides you with all the benefits of registering a device and in addition to this, it also changes the local state of a device. Changing the local state enables your users to sign-in to a device using an organizational work or school account instead of a personal account.

### Related topics
[Azure AD Joined](#Azure-AD-Joined), [Azure AD Registered](#Azure-AD-Registered), [Hybrid Azure AD Joined](#Hybrid-Azure-AD-Joined)

### More information
- [Introduction to device management in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-introduction)

[Return to Top](#Technology-and-Terms)
## Key Trust
The key trust model uses the user's Windows Hello for Business identity to authenticate to on-premises Active Directory.  The certificate trust model is supported in hybrid and on-premises deployments and requires Windows Server 2016 domain controllers.

### Related topics
[Certificate Trust](#Certificate-Trust), [Deployment Type](#Deployment-Type), [Hybrid Azure AD Joined](#Hybrid-Azure-AD-Joined), [Hybrid Deployment](#Hybrid-Deployment), [On-premises Deployment](#Onpremises-Deployment), [Trust Type](#Trust-Type), [Trust Type](#Trust-Type)

### More information
- [Windows Hello for Business Planning Guide](hello-planning-guide.md)

[Return to Top](#Technology-and-Terms)
## Managed Environment
Managed environments are for non-federated environments where Azure Active Directory manages the authentication using technologies such as Password Hash Synchronization and Pass-through Authentication rather than a federation service such as Active Directory Federation Services. 

### Related topics
[Federated Environment](#Federated-Environment), [Pass-through authentication](#Passthrough-authentication), [Password Hash Synchronization](#Password-Hash-Synchronization)

[Return to Top](#Technology-and-Terms)
## On-premises Deployment 
The Windows Hello for Business on-premises deployment is for organizations that exclusively have on-premises resources that are accessed using Active Directory identities.  On-premises deployments support domain joined devices.  The on-premises deployment model supports two authentication trust types, key trust and certificate trust.

### Related topics
[Cloud Deployment](#Cloud-Deployment), [Deployment Type](#Deployment-Type), [Hybrid Deployment](#Hybrid-Deployment)

### More information
- [Windows Hello for Business Planning Guide](hello-planning-guide.md)

[Return to Top](#Technology-and-Terms)
## Pass-through authentication
Provides a simple password validation for Azure AD authentication services using a software agent running on one or more on-premises servers to validate the users directly with your on-premises Active Directory. With pass-through authentication (PTA), you synchronize on-premises Active Directory user account objects with Office 365 and manage your users on-premises. Allows your users to sign in to both on-premises and Office 365 resources and applications using their on-premises account and password. This configuration validates users' passwords directly against your on-premises Active Directory without sending password hashes to Office 365. Companies with a security requirement to immediately enforce on-premises user account states, password policies, and logon hours would use this authentication method. With seamless single sign-on, users are automatically signed in to Azure AD when they are on their corporate devices and connected to your corporate network.

### Related topics
[Federated Environment](#Federated-Environment), [Managed Environment](#Managed-Environment), [Password Hash Synchronization](#Password-Hash-Synchronization)


### More information
- [Choosing the right authentication method for your Azure Active Directory hybrid identity solution](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)

[Return to Top](#Technology-and-Terms)
## Password Hash Sync
The simplest way to enable authentication for on-premises directory objects in Azure AD. With password hash sync (PHS), you synchronize your on-premises Active Directory user account objects with Office 365 and manage your users on-premises. Hashes of user passwords are synchronized from your on-premises Active Directory to Azure AD so that the users have the same password on-premises and in the cloud. When passwords are changed or reset on-premises, the new password hashes are synchronized to Azure AD so that your users can always use the same password for cloud resources and on-premises resources. The passwords are never sent to Azure AD or stored in Azure AD in clear text. Some premium features of Azure AD, such as Identity Protection, require PHS regardless of which authentication method is selected. With seamless single sign-on, users are automatically signed in to Azure AD when they are on their corporate devices and connected to your corporate network.

### Related topics
[Federated Environment](#Federated-Environment), [Managed Environment](#Managed-Environment), [Pass-through authentication](#Passthrough-authentication)

### More information
- [Choosing the right authentication method for your Azure Active Directory hybrid identity solution](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)

[Return to Top](#Technology-and-Terms)
## Primary Refresh Token
SSO relies on special tokens obtained for each of the types of applications above. These are in turn used to obtain access tokens to specific applications. In the traditional Windows Integrated authentication case using Kerberos, this token is a Kerberos TGT (ticket-granting ticket). For Azure AD and AD FS applications we call this a Primary Refresh Token (PRT). This is a [JSON Web Token](http://openid.net/specs/draft-jones-json-web-token-07.html) containing claims about both the user and the device.

The PRT is initially obtained during Windows Logon (user sign-in/unlock) in a similar way the Kerberos TGT is obtained. This is true for both Azure AD joined and domain joined devices. In personal devices registered with Azure AD, the PRT is initially obtained upon Add Work or School Account (in a personal device the account to unlock the device is not the work account but a consumer account e.g. hotmail.com, live.com, outlook.com, etc.).

The PRT is needed for SSO. Without it, the user will be prompted for credentials when accessing applications every time. Please also note that the PRT contains information about the device. This means that if you have any [device-based conditional access](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) policy set on an application, without the PRT, access will be denied.

[Return to Top](#Technology-and-Terms)
## Storage Root Key
The storage root key (SRK) is also an asymmetric key pair (RSA with a minimum of 2048 bits length). The SRK has a major role and is used to protect TPM keys, so that these keys cannot be used without the TPM. The SRK key is created when the ownership of the TPM is taken.

### Related topics
[Attestation Identity Keys](#Attestation-Identity-Keys), [Endorsement Key](#Endorsement-Key), [Trusted Platform Module](#Trusted-Platform-Module)

### More information
[TPM Library Specification](https://trustedcomputinggroup.org/resource/tpm-library-specification/)

[Return to Top](#Technology-and-Terms)
## Trust type
The trust type determines how a user authenticates to the Active Directory to access on-premises resources. There are two trust types, key trust and certificate trust.  The hybrid and on-premises deployment models support both trust types.  The trust type does not affect authentication to Azure Active Directory.  Windows Hello for Business authentication to Azure Active Directory always uses the key, not a certificate (excluding smart card authentication in a federated environment). 

### Related topics
[Certificate Trust](#Certificate-Trust), [Hybrid Deployment](#Hybrid-Deployment), [Key Trust](#Key-Trust), [On-premises Deployment](#Onpremises-Deployment)

### More information
- [Windows Hello for Business Planning Guide](hello-planning-guide.md)

[Return to Top](#Technology-and-Terms)
## Trusted Platform Module

A Trusted Platform Module (TPM) is a hardware component that provides unique security features.<br>

Windows 10 leverages security characteristics of a TPM for measuring boot integrity sequence (and based on that, unlocking automatically BitLocker protected drives), for protecting credentials or for health attestation.

A TPM implements controls that meet the specification described by the Trusted Computing Group (TCG). At the time of this writing, there are two versions of TPM specification produced by TCG that are not compatible with each other:
- The first TPM specification, version 1.2, was published in February 2005 by the TCG and standardized under ISO / IEC 11889 standard.
- The latest TPM specification, referred to as TPM 2.0, was released in April 2014 and has been approved by the ISO/IEC Joint Technical Committee (JTC) as ISO/IEC 11889:2015.

Windows�10 uses the TPM for cryptographic calculations as part of health attestation and to protect the keys for BitLocker, Windows Hello, virtual smart cards, and other public key certificates. For more information, see [TPM requirements in Windows 10](https://go.microsoft.com/fwlink/p/?LinkId=733948).

Windows�10 recognizes versions 1.2 and 2.0 TPM specifications produced by the TCG. For the most recent and modern security features, Windows�10 supports only TPM 2.0. 

TPM 2.0 provides a major revision to the capabilities over TPM 1.2:

- Update cryptography strength to meet modern security needs
    - Support for SHA-256 for PCRs
    - Support for HMAC command
- Cryptographic algorithms flexibility to support government needs
    - TPM 1.2 is severely restricted in terms of what algorithms it can support
    - TPM 2.0 can support arbitrary algorithms with minor updates to the TCG specification documents
- Consistency across implementations
    - The TPM 1.2 specification allows vendors wide latitude when choosing implementation details
    - TPM 2.0 standardizes much of this behavior

In a simplified manner, the TPM is a passive component with limited resources. It can calculate random numbers, RSA keys, decrypt short data, store hashes taken when booting the device.  A TPM incorporates in a single component:
- A RSA 2048-bit key generator
- A random number generator
- Nonvolatile memory for storing EK, SRK, and AIK keys
- A cryptographic engine to encrypt, decrypt, and sign
- Volatile memory for storing the PCRs and RSA keys


### Related topics
[Attestation Identity Keys](#Attestation-Identity-Keys), [Endorsement Key](#Endorsement-Key), [Storage Root Key](#Storage-Root-Key)

### More information
- [TPM Library Specification](https://trustedcomputinggroup.org/resource/tpm-library-specification/)

[Return to Top](#Technology-and-Terms)















    