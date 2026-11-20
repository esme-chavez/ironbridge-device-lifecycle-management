# Device Provisioning, Compliance & Retirement (Intune)(MDM)

**Ironbridge Bank & Trust | MRA Finding #2 | Simulated enterprise environment**

---

## Video Walkthrough

**[▶ Watch the 3 to 7 minute walkthrough](ADD_YOUR_VIDEO_LINK_HERE)**

---

## Problem Statement

On a Friday afternoon, a mortgage officer at Ironbridge Bank & Trust had his laptop stolen out of his truck. On it were loan application packets containing customer names, social security numbers and bank statements. Asked whether the drive was encrypted, he answered: "It has a password when it turns on, does that count?"

Nobody at the bank could answer the question, because the bank had no device inventory, no enforced encryption, and no ability to lock or wipe anything remotely. Leadership then asked the question that mattered even more: how many other laptops are out there like that one? There was no way to know.

The finding I was assigned required centralized device management enforcing encryption, compliance policies, and remote lock and wipe across all corporate endpoints.

The part that stood out to me the most was that this device put the bank at a high level of risk. No one in the organization was able to identify which device had been stolen, and because of that, it could not be remotely wiped or blocked from access. As a result, any sensitive information stored on the device could easily be accessed by whoever had it, potentially leading to a HIPAA breach and putting the company at serious risk.

---

## Solution Overview

I deployed Microsoft Intune from a Microsoft 365 Business Premium tenant and brought a corporate Windows 11 endpoint under management.

I configured automatic enrollment at the tenant level so devices join management without depending on anyone remembering a step. I built configuration policies enforcing BitLocker encryption with recovery key escrow to Entra ID, and a five minute screen lock. I then built a compliance baseline requiring BitLocker, Secure Boot, antivirus, a password to unlock, and a minimum OS version.

Finally I validated the control by breaking compliance deliberately, watching the device fall out of compliance, remediating it, and confirming it returned to a trusted state. I then exported inventory, compliance and encryption evidence and executed remote lock and remote wipe.

---

## Key Work

**Enrolled a device and gave it an identity.** Joined a Windows 11 Pro endpoint to Entra ID and confirmed the join with `dsregcmd /status`. A device the organization cannot identify is a device it cannot protect.

**Enforced encryption with key escrow.** Configured BitLocker through Intune with silent enablement and recovery keys stored in Entra ID. Verified encryption at the device level with `manage-bde -status` rather than trusting the policy screen.

**Separated configuration from compliance.** Configuration sets the device the way the bank wants it. Compliance independently checks whether it actually is. Pushing a policy is not evidence, and only the second one produces an answer an examiner will accept.

**Proved the control works.** Disabled encryption on the device on purpose, watched Intune mark it noncompliant and name the failed setting, remediated it, and confirmed it returned to compliant.
During this process, it took about three hours to complete because disabling the encryption took some time. I also ran into a few issues along the way. When I tried to disable the encryption, the status would not update or change. After doing some troubleshooting, I figured out that after enabling or disabling the encryption and syncing the account, I needed to shut down the virtual machine before the encryption status would update correctly.
**Built the inventory the bank never had.** Exported device, compliance and encryption reports, which answer in seconds the question leadership could not answer at all.

**Executed remote lock and wipe.** The capability that did not exist on the day the laptop was stolen.

---

## Tools Used

Microsoft Intune, Microsoft Entra ID, Microsoft 365 Business Premium, Windows 11 Pro, BitLocker, VirtualBox or UTM or Hyper-V.

---

## Repository Contents

- `policies/` - compliance baseline and configuration policy definitions with the reasoning for each requirement
- `evidence/` - exported device inventory, compliance and encryption reports
- `disposal-procedure.md` - the device retirement and secure disposal procedure I wrote
- `screenshots/` - visual evidence at each stage of the device lifecycle

---

## What I Learned

**A password is not encryption.**
A login password guards the doorway to the operating system, but a thief can pull the drive out and read it on another machine and steal all sensitive information from the bank. This could expose customer data and leave the bank vulnerable to lawsuits and other serious consequences. Encryption helps protect the data stored on a device by making it unreadable to unauthorized users. This was important because it determined whether the stolen device posed a risk to sensitive information. 


**Compliance is all or nothing.**
A device that passes four checks but fails one is still considered noncompliant. Even a single failed requirement can create a security risk. This check helps identify whether a device is up to date with Windows or macOS updates, allowing us to make sure it is protected against known vulnerabilities and does not pose a risk to the network.


**Device compliance feeds an access decision.**
Device compliance plays a role in access decisions. It is not just a report that gets reviewed and filed away. Instead, it helps determine whether a device is allowed to access sensitive systems and company data. If compliance policies had been in place for the stolen laptop, the situation could have been very different. The device could have been blocked from accessing company resources once it was identified as noncompliant or missing. This would have reduced the risk of unauthorized access to sensitive information, even if someone had possession of the laptop, because they would still need authorized credentials and a compliant device to gain access.


---

*Built as part of the TotalThreat IT & IAM Career Accelerator. Ironbridge Bank & Trust is a simulated enterprise environment and not a real employer. The tenant, policies, evidence and documentation in this repository are my own work.*
