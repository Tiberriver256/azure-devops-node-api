# Troubleshooting Decision Tree

> **Navigation**: [Home](../index.md) > Decision Tree

This decision tree helps you navigate to the right solution based on the symptoms you're experiencing. Start at the top and follow the branches that match your situation.

## Authentication and Connection Issues

**Are you having trouble connecting to Azure DevOps?**

* **Yes, I can't authenticate at all**
  * Do you get a 401 Unauthorized error?
    * Yes → [Invalid Token](./problem-categories/authentication-issues.md#invalid-token)
    * No → Continue below
  * Did your authentication stop working suddenly?
    * Yes → [Token Expiration](./problem-categories/authentication-issues.md#token-expiration)
    * No → Continue below
  * Are you using an account with 2FA enabled?
    * Yes → [Two-Factor Authentication Issues](./problem-categories/authentication-issues.md#two-factor-authentication-issues)
    * No → Continue below
  * Does your organization use SSO or a custom identity provider?
    * Yes → [Identity Provider Problems](./problem-categories/authentication-issues.md#identity-provider-problems)
    * No → [See Authentication Issues](./problem-categories/authentication-issues.md)
* **Yes, I can authenticate but have connection issues**
  * Do you get timeout errors?
    * Yes → [Connection Timeout](./problem-categories/connection-problems.md#connection-timeout)
    * No → Continue below
  * Are you behind a corporate firewall or proxy?
    * Yes → [Proxy Configuration](./problem-categories/connection-problems.md#proxy-configuration)
    * No → Continue below
  * Do you get 404 Not Found errors?
    * Yes → [Incorrect Endpoint](./problem-categories/connection-problems.md#incorrect-endpoint)
    * No → [See Connection Problems](./problem-categories/connection-problems.md)
* **No, I can connect but have other issues** → Continue to next section

## Permission and Access Issues

**Can you connect but can't access specific resources?**

* **Yes, I get 403 Forbidden errors**
  * Are you using a token with limited scopes?
    * Yes → [Insufficient Scopes](./problem-categories/authentication-issues.md#insufficient-scopes)
    * No → Continue below
  * Are you trying to access resources you don't own?
    * Yes → [Resource Access](./problem-categories/permission-errors.md#resource-access)
    * No → Continue below
  * Are you trying to write to a protected branch?
    * Yes → [Branch Policies](./problem-categories/permission-errors.md#branch-policies)
    * No → [See Permission Errors](./problem-categories/permission-errors.md)
* **No, I have other issues** → Continue to next section

## Data and API Usage Issues

**Are you having issues with API operations?**

* **Yes, I can't retrieve data correctly**
  * Do you get empty results when you expect data?
    * Yes → [Empty Results](./problem-categories/data-retrieval-issues.md#empty-results)
    * No → Continue below
  * Are you using queries or filters?
    * Yes → [Query Syntax](./problem-categories/data-retrieval-issues.md#query-syntax)
    * No → Continue below
  * Are you getting unexpected pagination?
    * Yes → [Pagination Issues](./problem-categories/data-retrieval-issues.md#pagination-issues)
    * No → [See Data Retrieval Issues](./problem-categories/data-retrieval-issues.md)
* **Yes, I can't create or update data**
  * Do you get 400 Bad Request errors?
    * Yes → [Malformed Data](./problem-categories/data-modification-errors.md#malformed-data)
    * No → Continue below
  * Do you get validation errors?
    * Yes → [Validation Failures](./problem-categories/data-modification-errors.md#validation-failures)
    * No → Continue below
  * Are you hitting size limits?
    * Yes → [Size Limits](./problem-categories/data-modification-errors.md#size-limits)
    * No → [See Data Modification Errors](./problem-categories/data-modification-errors.md)
* **Yes, I'm experiencing performance issues**
  * Do you get 429 Too Many Requests errors?
    * Yes → [Rate Limiting](./problem-categories/performance-issues.md#rate-limiting)
    * No → Continue below
  * Are operations taking too long?
    * Yes → [Slow Operations](./problem-categories/performance-issues.md#slow-operations)
    * No → Continue below
  * Do you have connection drops during long operations?
    * Yes → [Connection Stability](./problem-categories/performance-issues.md#connection-stability)
    * No → [See Performance Issues](./problem-categories/performance-issues.md)
* **No, I have other issues** → [Advanced Troubleshooting](./advanced-troubleshooting.md)

## Configuration and Environment Issues

**Are you experiencing configuration issues?**

* **Yes, I'm having issues with API versions**
  * Are you getting deprecation warnings?
    * Yes → [API Deprecation](./problem-categories/configuration-problems.md#api-deprecation)
    * No → Continue below
  * Are features missing that should exist?
    * Yes → [Version Mismatch](./problem-categories/configuration-problems.md#version-mismatch)
    * No → Continue below
* **Yes, I'm having issues with environment setup**
  * Are you getting library or dependency errors?
    * Yes → [Dependency Issues](./problem-categories/configuration-problems.md#dependency-issues)
    * No → Continue below
  * Are you having issues with Node.js versions?
    * Yes → [Runtime Compatibility](./problem-categories/configuration-problems.md#runtime-compatibility)
    * No → [See Configuration Problems](./problem-categories/configuration-problems.md)
* **I still can't identify my issue** → [Contact Support](./getting-help.md)

## Interactive Decision Tool

<details>
<summary><b>Click here to use the interactive troubleshooting wizard</b></summary>

**What best describes your issue?**

- [ ] I can't authenticate to Azure DevOps
  - [Go to Authentication Issues](./problem-categories/authentication-issues.md)
- [ ] I can authenticate but can't connect properly
  - [Go to Connection Problems](./problem-categories/connection-problems.md)
- [ ] I can connect but get permission errors
  - [Go to Permission Errors](./problem-categories/permission-errors.md)
- [ ] I'm having trouble retrieving data
  - [Go to Data Retrieval Issues](./problem-categories/data-retrieval-issues.md)
- [ ] I can't create or update data
  - [Go to Data Modification Errors](./problem-categories/data-modification-errors.md)
- [ ] My API calls are too slow or I'm being rate limited
  - [Go to Performance Issues](./problem-categories/performance-issues.md)
- [ ] I'm having issues with setup or configuration
  - [Go to Configuration Problems](./problem-categories/configuration-problems.md)
- [ ] None of the above or I need more help
  - [Go to Advanced Troubleshooting](./advanced-troubleshooting.md)

</details>

## Common Error Codes

If you have a specific error code, you can look it up directly:

| Error Code | Error Type | Solution Link |
|------------|------------|--------------|
| 401 | Unauthorized | [Authentication Issues](./problem-categories/authentication-issues.md) |
| 403 | Forbidden | [Permission Errors](./problem-categories/permission-errors.md) |
| 404 | Not Found | [Incorrect Endpoint](./problem-categories/connection-problems.md#incorrect-endpoint) |
| 400 | Bad Request | [Malformed Data](./problem-categories/data-modification-errors.md#malformed-data) |
| 429 | Too Many Requests | [Rate Limiting](./problem-categories/performance-issues.md#rate-limiting) |
| 500 | Server Error | [Service Issues](./getting-help.md#service-status) |
| 503 | Service Unavailable | [Service Issues](./getting-help.md#service-status) |

For a complete list of error codes and their explanations, see the [Error Code Reference](./error-code-reference.md).

---

<div align="left">
  <a href="../quick-solutions.md">← Back to Quick Solutions</a>
</div>

<div align="right">
  <a href="./issue-identification.md">Next: Issue Identification →</a>
</div> 