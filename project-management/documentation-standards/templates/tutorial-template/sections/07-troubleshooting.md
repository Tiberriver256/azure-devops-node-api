# Troubleshooting

This section addresses common issues you might encounter when implementing this tutorial.

## Common Issues

### Problem: 401 Unauthorized Error When Connecting
**Solution:** Your personal access token may have expired or doesn't have sufficient permissions. Generate a new token with the appropriate scopes (typically "{required scopes}").

### Problem: Cannot Find Module Error
**Solution:** Ensure you have installed all the required packages using npm:
```bash
npm install azure-devops-node-api @types/node typescript
```

### Problem: {Common Error Message}
**Solution:** {Step-by-step solution}

## Debugging Tips

### Enabling Verbose Logging

To get more detailed information about what's happening in your application, add the following code at the beginning of your script:

```typescript
// Enable debug logging
process.env.AZURE_DEVOPS_NODE_API_DEBUG = "true";
```

### Checking Network Connectivity

If you're experiencing connection issues, verify that:

1. Your network can reach Azure DevOps (no firewall blocking access)
2. Your organization URL is correct
3. Your personal access token has the correct permissions

### Inspecting API Responses

To inspect the full API response for debugging:

```typescript
const result = await api.someMethod();
console.log(JSON.stringify(result, null, 2)); // Pretty print the result
```

## Getting Help

If you're still experiencing issues:

1. Check the [Azure DevOps Node API GitHub repository](https://github.com/Microsoft/azure-devops-node-api/issues) for similar issues
2. Review the [Azure DevOps REST API documentation](https://docs.microsoft.com/en-us/rest/api/azure/devops/?view=azure-devops-rest-6.0) for the endpoints you're using
3. Post a question on [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-devops-node-api) with the `azure-devops-node-api` tag

---

**Navigation**:
- [Back to Index](../index.md)
- Previous: [Complete Example](./06-complete-example.md)
- Next: [Next Steps & Resources](./08-next-steps-resources.md) 