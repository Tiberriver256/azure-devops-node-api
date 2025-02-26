# Troubleshooting Decision Tree

> **Navigation**: [Home](../index.md) > Decision Tree

This decision tree helps you navigate to the right solution based on the symptoms you're experiencing. Start at the top and follow the branches that match your situation.

## Authentication and Connection Issues

**Are you having trouble connecting to Azure DevOps?**

* **Yes, I can't authenticate at all**
  * Do you get a 401 Unauthorized error?
    * Yes → [Invalid Token](./problem-categories/authentication-issues.md#invalid-token) | ⏱️ 5 min | 🔴 High Severity
    * No → Continue below
  * Did your authentication stop working suddenly?
    * Yes → [Token Expiration](./problem-categories/authentication-issues.md#token-expiration) | ⏱️ 5 min | 🟡 Medium Severity
    * No → Continue below
  * Are you using an account with 2FA enabled?
    * Yes → [Two-Factor Authentication Issues](./problem-categories/authentication-issues.md#two-factor-authentication-issues) | ⏱️ 10 min | 🟡 Medium Severity
    * No → Continue below
  * Does your organization use SSO or a custom identity provider?
    * Yes → [Identity Provider Problems](./problem-categories/authentication-issues.md#identity-provider-problems) | ⏱️ 15 min | 🔴 High Severity
    * No → [See Authentication Issues](./problem-categories/authentication-issues.md)
* **Yes, I can authenticate but have connection issues**
  * Do you get timeout errors?
    * Yes → [Connection Timeout](./problem-categories/connection-problems.md#connection-timeout) | ⏱️ 5 min | 🟡 Medium Severity
    * No → Continue below
  * Are you behind a corporate firewall or proxy?
    * Yes → [Proxy Configuration](./problem-categories/connection-problems.md#proxy-configuration) | ⏱️ 10 min | 🟡 Medium Severity
    * No → Continue below
  * Do you get 404 Not Found errors?
    * Yes → [Incorrect Endpoint](./problem-categories/connection-problems.md#incorrect-endpoint) | ⏱️ 3 min | 🟢 Low Severity
    * No → [See Connection Problems](./problem-categories/connection-problems.md)
* **No, I can connect but have other issues** → Continue to next section

## Permission and Access Issues

**Can you connect but can't access specific resources?**

* **Yes, I get 403 Forbidden errors**
  * Are you using a token with limited scopes?
    * Yes → [Insufficient Scopes](./problem-categories/authentication-issues.md#insufficient-scopes) | ⏱️ 5 min | 🟡 Medium Severity
    * No → Continue below
  * Are you trying to access resources you don't own?
    * Yes → [Resource Access](./problem-categories/permission-errors.md#resource-access) | ⏱️ 10 min | 🔴 High Severity
    * No → Continue below
  * Are you trying to write to a protected branch?
    * Yes → [Branch Policies](./problem-categories/permission-errors.md#branch-policies) | ⏱️ 8 min | 🟡 Medium Severity
    * No → [See Permission Errors](./problem-categories/permission-errors.md)
* **No, I have other issues** → Continue to next section

## Data and API Usage Issues

**Are you having issues with API operations?**

* **Yes, I can't retrieve data correctly**
  * Do you get empty results when you expect data?
    * Yes → [Empty Results](./problem-categories/data-retrieval-issues.md#empty-results) | ⏱️ 5 min | 🟢 Low Severity
    * No → Continue below
  * Are you using queries or filters?
    * Yes → [Query Syntax](./problem-categories/data-retrieval-issues.md#query-syntax) | ⏱️ 8 min | 🟡 Medium Severity
    * No → Continue below
  * Are you getting unexpected pagination?
    * Yes → [Pagination Issues](./problem-categories/data-retrieval-issues.md#pagination-issues) | ⏱️ 10 min | 🟡 Medium Severity
    * No → [See Data Retrieval Issues](./problem-categories/data-retrieval-issues.md)
* **Yes, I can't create or update data**
  * Do you get 400 Bad Request errors?
    * Yes → [Malformed Data](./problem-categories/data-modification-errors.md#malformed-data) | ⏱️ 10 min | 🟡 Medium Severity
    * No → Continue below
  * Do you get validation errors?
    * Yes → [Validation Failures](./problem-categories/data-modification-errors.md#validation-failures) | ⏱️ 12 min | 🟡 Medium Severity
    * No → Continue below
  * Are you hitting size limits?
    * Yes → [Size Limits](./problem-categories/data-modification-errors.md#size-limits) | ⏱️ 5 min | 🟢 Low Severity
    * No → [See Data Modification Errors](./problem-categories/data-modification-errors.md)
* **Yes, I'm experiencing performance issues**
  * Do you get 429 Too Many Requests errors?
    * Yes → [Rate Limiting](./problem-categories/performance-issues.md#rate-limiting) | ⏱️ 15 min | 🔴 High Severity
    * No → Continue below
  * Are operations taking too long?
    * Yes → [Slow Operations](./problem-categories/performance-issues.md#slow-operations) | ⏱️ 20 min | 🟡 Medium Severity
    * No → Continue below
  * Do you have connection drops during long operations?
    * Yes → [Connection Stability](./problem-categories/performance-issues.md#connection-stability) | ⏱️ 15 min | 🔴 High Severity
    * No → [See Performance Issues](./problem-categories/performance-issues.md)
* **No, I have other issues** → [Advanced Troubleshooting](./advanced-troubleshooting.md)

## Configuration and Environment Issues

**Are you experiencing configuration issues?**

* **Yes, I'm having issues with API versions**
  * Are you getting deprecation warnings?
    * Yes → [API Deprecation](./problem-categories/configuration-problems.md#api-deprecation) | ⏱️ 5 min | 🟡 Medium Severity
    * No → Continue below
  * Are features missing that should exist?
    * Yes → [Version Mismatch](./problem-categories/configuration-problems.md#version-mismatch) | ⏱️ 10 min | 🟡 Medium Severity
    * No → Continue below
* **Yes, I'm having issues with environment setup**
  * Are you getting library or dependency errors?
    * Yes → [Dependency Issues](./problem-categories/configuration-problems.md#dependency-issues) | ⏱️ 15 min | 🟡 Medium Severity
    * No → Continue below
  * Are you having issues with Node.js versions?
    * Yes → [Runtime Compatibility](./problem-categories/configuration-problems.md#runtime-compatibility) | ⏱️ 10 min | 🟢 Low Severity
    * No → [See Configuration Problems](./problem-categories/configuration-problems.md)
* **I still can't identify my issue** → [Contact Support](./getting-help.md)

## Interactive Decision Tool

<details>
<summary><b>Click here to use the interactive troubleshooting wizard</b></summary>

**What best describes your issue?**

- [ ] I can't authenticate to Azure DevOps | 🔴 High Severity
  - [Go to Authentication Issues](./problem-categories/authentication-issues.md)
- [ ] I can authenticate but can't connect properly | 🟡 Medium Severity
  - [Go to Connection Problems](./problem-categories/connection-problems.md)
- [ ] I can connect but get permission errors | 🔴 High Severity
  - [Go to Permission Errors](./problem-categories/permission-errors.md)
- [ ] I'm having trouble retrieving data | 🟡 Medium Severity
  - [Go to Data Retrieval Issues](./problem-categories/data-retrieval-issues.md)
- [ ] I can't create or update data | 🟡 Medium Severity
  - [Go to Data Modification Errors](./problem-categories/data-modification-errors.md)
- [ ] My API calls are too slow or I'm being rate limited | 🟡 Medium Severity
  - [Go to Performance Issues](./problem-categories/performance-issues.md)
- [ ] I'm having issues with setup or configuration | 🟢 Low Severity
  - [Go to Configuration Problems](./problem-categories/configuration-problems.md)
- [ ] None of the above or I need more help
  - [Go to Advanced Troubleshooting](./advanced-troubleshooting.md)

</details>

## Common Error Codes

If you have a specific error code, you can look it up directly:

| Error Code | Error Type | Solution Link | Severity |
|------------|------------|--------------|----------|
| 401 | Unauthorized | [Authentication Issues](./problem-categories/authentication-issues.md) | 🔴 High |
| 403 | Forbidden | [Permission Errors](./problem-categories/permission-errors.md) | 🔴 High |
| 404 | Not Found | [Incorrect Endpoint](./problem-categories/connection-problems.md#incorrect-endpoint) | 🟢 Low |
| 400 | Bad Request | [Malformed Data](./problem-categories/data-modification-errors.md#malformed-data) | 🟡 Medium |
| 429 | Too Many Requests | [Rate Limiting](./problem-categories/performance-issues.md#rate-limiting) | 🔴 High |
| 500 | Server Error | [Service Issues](./getting-help.md#service-status) | 🔴 High |
| 503 | Service Unavailable | [Service Issues](./getting-help.md#service-status) | 🔴 High |

For a complete list of error codes and their explanations, see the [Error Code Reference](./error-code-reference.md).

## Diagnostic Tools

Need to gather more information about your issue? Use our [Diagnostic Tools](./diagnostic-tools.md) to help identify the root cause.

---

<div align="left">
  <a href="../index.md#quick-solutions">← Back to Quick Solutions</a>
</div>

<div align="right">
  <a href="./advanced-troubleshooting.md">Next: Advanced Troubleshooting →</a>
</div>

<div align="right">
<i>Last updated: February 26, 2024</i>
</div> 