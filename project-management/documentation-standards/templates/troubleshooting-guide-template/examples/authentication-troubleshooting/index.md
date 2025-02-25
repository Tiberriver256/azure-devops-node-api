# Authentication Troubleshooting Guide

> This guide helps you identify and resolve authentication-related issues with the Azure DevOps Node API.

## 🔎 How to Use This Guide

1. If you know your specific authentication issue, use the navigation below to go directly to a solution
2. For quick fixes to common authentication problems, check the [Quick Solutions](./quick-solutions.md) page
3. If you're unsure what's wrong, use the [Issue Identification](./sections/issue-identification.md) page or [Decision Tree](./sections/decision-tree.md)
4. For authentication error code explanations, refer to the [Error Code Reference](./sections/error-code-reference.md)

## 📋 Common Authentication Issue Categories

| Category | Description |
|----------|-------------|
| 🔑 [Token Issues](./sections/problem-categories/token-issues.md) | Problems with PATs, token formatting, and expiration |
| 👤 [Identity Issues](./sections/problem-categories/identity-issues.md) | AAD, MSA, and identity provider problems |
| 🔒 [Scope & Permission Issues](./sections/problem-categories/scope-permission-issues.md) | Token scope and permission problems |

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

## 🛠️ Quick Authentication Solutions

See our [Quick Solutions](./quick-solutions.md) page for immediate fixes to the most common authentication issues, including:

- Invalid or improperly formatted PAT tokens
- Token expiration issues
- Two-factor authentication problems
- Common authentication error codes

## 🔍 Common Authentication Error Codes

If you're seeing a specific error code related to authentication, check these common solutions:

| Error Code | Error Message | Solution Link |
|------------|---------------|--------------|
| 401 | TF400813: The user is not authorized to access this resource | [Invalid Token](./sections/problem-categories/token-issues.md#invalid-token) |
| 401 | VS402965: The token has expired | [Token Expiration](./sections/problem-categories/token-issues.md#token-expiration) |
| 403 | TF400813: The user [...] lacks permission | [Insufficient Scopes](./sections/problem-categories/scope-permission-issues.md#insufficient-scopes) |
| 401 | AADSTS50126: Invalid username or password | [AAD Authentication Issues](./sections/problem-categories/identity-issues.md#azure-active-directory-issues) |

For more error codes and detailed explanations, see the [Error Code Reference](./sections/error-code-reference.md).

## 🆘 Need More Help with Authentication?

If you can't find a solution for your authentication issue in this guide:

- Check our [Getting Help](./sections/getting-help.md) page
- Review the [Azure DevOps Authentication Documentation](https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)
- Submit an issue on our [GitHub repository](https://github.com/microsoft/azure-devops-node-api/issues)

---

<details>
<summary><b>About This Example</b></summary>

This is an example implementation of the Troubleshooting Guide Template focusing on authentication issues. It demonstrates:

- The multi-file structure for better navigation
- Progressive disclosure of information
- Consistent problem-solution formatting
- User-friendly navigation patterns
- GitHub-compatible interactive elements

</details> 