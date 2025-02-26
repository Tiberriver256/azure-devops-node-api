**Navigation**: [Home](../../../../index.md) > [Documentation](../../../index.md) > [Troubleshooting](../../index.md) > Authentication Issues

# Authentication Troubleshooting Guide

> This guide helps you identify and resolve authentication-related issues with the Azure DevOps Node API.

## Contents

- [How to Use This Guide](#-how-to-use-this-guide)
- [Common Authentication Issue Categories](#-common-authentication-issue-categories)
- [Authentication Decision Tree](#-authentication-decision-tree)
- [Quick Authentication Solutions](#-quick-authentication-solutions)
- [Common Authentication Error Codes](#-common-authentication-error-codes)
- [Need More Help](#-need-more-help-with-authentication)

---

## 🔎 How to Use This Guide

1. If you know your specific authentication issue, use the navigation below to go directly to a solution
2. For quick fixes to common authentication problems, check the [Quick Solutions](./quick-solutions.md) page
3. If you're unsure what's wrong, use the [Issue Identification](./sections/issue-identification.md) page or [Decision Tree](./sections/decision-tree.md)
4. For authentication error code explanations, refer to the [Error Code Reference](./sections/error-code-reference.md)

---

## 📋 Common Authentication Issue Categories

| Category | Severity | Est. Resolution Time | Description |
|----------|----------|----------------------|-------------|
| 🔑 [Token Issues](./sections/problem-categories/token-issues.md) | 🔴 High | ⏱️ 5-15 minutes | Problems with PATs, token formatting, and expiration |
| 👤 [Identity Issues](./sections/problem-categories/identity-issues.md) | 🟡 Medium | ⏱️ 15-30 minutes | AAD, MSA, and identity provider problems |
| 🔒 [Scope & Permission Issues](./sections/problem-categories/scope-permission-issues.md) | 🟡 Medium | ⏱️ 10-20 minutes | Token scope and permission problems |

---

## 🔍 Authentication Decision Tree

Not sure what's causing your authentication issue? Use our [Authentication Decision Tree](./sections/decision-tree.md) to navigate to the right solution:

<details>
<summary><b>Quick Decision Tree Preview</b> (click to expand)</summary>

**What authentication issue are you experiencing?**

* **Invalid token or credentials**
  * Recently generated token not working → [Check token format](./sections/problem-categories/token-issues.md#token-format-problems)
  * Previously working token now failing → [Check token expiration](./sections/problem-categories/token-issues.md#token-expiration)

* **Identity or account problems**
  * Using Microsoft Account (MSA) → [MSA Issues](./sections/problem-categories/identity-issues.md#microsoft-account-issues)
  * Using Azure Active Directory → [AAD Issues](./sections/problem-categories/identity-issues.md#azure-active-directory-issues)
  * Using SSO or custom identity provider → [Identity Provider Problems](./sections/problem-categories/identity-issues.md#identity-provider-problems)

* **Permission or access problems**
  * Can authenticate but can't access resource → [Insufficient Scopes](./sections/problem-categories/scope-permission-issues.md#insufficient-scopes)
  * Getting 401 errors for specific resources → [Resource Permissions](./sections/problem-categories/scope-permission-issues.md#resource-permissions)
</details>

---

## 🛠️ Quick Authentication Solutions

See our [Quick Solutions](./quick-solutions.md) page for immediate fixes to the most common authentication issues, including:

<details>
<summary><b>🔴 High Priority Issues</b> (click to expand)</summary>

- **Invalid PAT token format** (⏱️ 5 minutes)
  - Ensure no extra whitespace in token
  - Check base64 encoding format
  - Verify correct implementation in auth handler
  
- **Token expiration issues** (⏱️ 5 minutes)
  - Generate a new token
  - Extend expiration period in organization settings
  - Implement token refresh strategy

</details>

<details>
<summary><b>🟡 Medium Priority Issues</b> (click to expand)</summary>

- **Two-factor authentication problems** (⏱️ 15 minutes)
  - Complete 2FA verification process
  - Use device code flow for interactive authentication
  - Check organization 2FA policies
  
- **Identity provider configuration** (⏱️ 20 minutes)
  - Verify Azure AD tenant configuration
  - Check federation settings
  - Review conditional access policies

</details>

---

## 🔍 Common Authentication Error Codes

If you're seeing a specific error code related to authentication, check these common solutions:

| Error Code | Severity | Error Message | Solution Link |
|------------|----------|---------------|--------------|
| 401 | 🔴 High | TF400813: The user is not authorized to access this resource | [Invalid Token](./sections/problem-categories/token-issues.md#invalid-token) |
| 401 | 🔴 High | VS402965: The token has expired | [Token Expiration](./sections/problem-categories/token-issues.md#token-expiration) |
| 403 | 🟡 Medium | TF400813: The user [...] lacks permission | [Insufficient Scopes](./sections/problem-categories/scope-permission-issues.md#insufficient-scopes) |
| 401 | 🟡 Medium | AADSTS50126: Invalid username or password | [AAD Authentication Issues](./sections/problem-categories/identity-issues.md#azure-active-directory-issues) |

📝 **Note**: For more error codes and detailed explanations, see the [Error Code Reference](./sections/error-code-reference.md).

---

## 🆘 Need More Help with Authentication?

If you can't find a solution for your authentication issue in this guide:

- Check our [Getting Help](./sections/getting-help.md) page
- Use the [🔧 Diagnostic Tools](./sections/diagnostic-tools.md) to collect more information
- Review the [Azure DevOps Authentication Documentation](https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)
- Submit an issue on our [GitHub repository](https://github.com/microsoft/azure-devops-node-api/issues)

💡 **Tip**: When submitting an authentication issue, include diagnostic information from the [Diagnostic Tools](./sections/diagnostic-tools.md) section to help maintainers resolve your issue more quickly.

---

## See Also

- [Token-Based Authentication Concepts](../../../concepts/authentication.md)
- [Working with OAuth](../../../guides/oauth-authentication.md)
- [Authentication API Reference](../../../api-reference/authentication/README.md)
- [Security Best Practices](../../../guides/security-best-practices.md)

---

<details>
<summary><b>About This Example</b></summary>

This is an example implementation of the Troubleshooting Guide Template focusing on authentication issues. It demonstrates:
- The multi-file structure for better navigation
- Progressive disclosure of information
- Consistent problem-solution formatting
- User-friendly navigation patterns
- GitHub-compatible interactive elements
- Severity indicators and resolution time estimates
</details>