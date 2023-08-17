# Unable to join computer to Azure Active Directory Domain Services (AADDS).

## Issue

After installing Azure Active Directory Domain Services (AADDS) and trying to domain join a computer using a domain admin/enterprise admin account, I received a message that password was incorrect/account is locked out.

## Solution

After ADDS is set up, intital account passwords are not synchronized. To resolve this, change the password of your domain admin account.

## References

[Troubleshoot account sign-in problems with an Azure Active Directory Domain Services managed domain | MS-Learn](https://learn.microsoft.com/en-us/azure/active-directory-domain-services/troubleshoot-sign-in)