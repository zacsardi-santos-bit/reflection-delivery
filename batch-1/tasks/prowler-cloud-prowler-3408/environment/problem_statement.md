## Description

We need to add several new security compliance checks for Azure virtual machines to improve cloud security posture coverage. Currently, the tool has no checks to verify whether Azure VM disks are using customer-managed encryption or whether VMs themselves are using managed disks. Additionally, there is no check to confirm that endpoint protection software is installed on VMs through cloud security assessment results.

## Expected Behavior

- A new check should determine whether disks that are currently attached to virtual machines are encrypted using customer-managed keys. Disks using only platform-managed keys should be flagged as non-compliant. Unattached disks should not be included in this check's scope.
- A separate new check should determine whether disks that are NOT attached to any virtual machine are encrypted using customer-managed keys. Attached disks should not be included in this check's scope.
- A new check should verify that each virtual machine is using managed disks for both its operating system disk and any data disks. A VM should fail if any disk (OS or data) is unmanaged.
- A new check should use security assessment data to determine whether endpoint protection solutions have been installed on virtual machines. The result should reflect whether the security recommendation is in a healthy or unhealthy state.
- A new Azure virtual machine service layer should be created to retrieve and model virtual machine and disk data from Azure subscriptions, including encryption type and attachment state for disks.

## Why This Matters

These checks fill important gaps in Azure security posture monitoring. Using customer-managed encryption keys gives organizations control over their data protection, and ensuring endpoint protection is active reduces the attack surface of virtual machines. Without these checks, teams have no automated way to audit these critical security configurations across their Azure subscriptions.
