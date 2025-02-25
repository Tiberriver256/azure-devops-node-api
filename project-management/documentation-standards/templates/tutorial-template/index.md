# Tutorial Template: Multi-File Structure

This template provides a standardized format for creating step-by-step guides that help users accomplish specific tasks using the Azure DevOps Node API. The template has been divided into multiple files for easier navigation and management.

## Template Structure

| Section | Description |
|---------|-------------|
| [1. Header and Overview](./sections/01-header-overview.md) | Title, difficulty level, time estimate, introduction, and overview |
| [2. Prerequisites](./sections/02-prerequisites.md) | Required software, accounts, permissions, and knowledge |
| [3. Basic Implementation](./sections/03-basic-implementation.md) | Step-by-step guide for creating a minimal working example |
| [4. Advanced Features](./sections/04-advanced-features.md) | Additional functionality to enhance the basic implementation |
| [5. Common Scenarios](./sections/05-common-scenarios.md) | Real-world use cases with code examples |
| [6. Complete Example](./sections/06-complete-example.md) | Full code example combining all concepts |
| [7. Troubleshooting](./sections/07-troubleshooting.md) | Common issues and their solutions |
| [8. Next Steps & Resources](./sections/08-next-steps-resources.md) | Related documentation and further learning |

## How to Use This Template

1. Create a new directory for your tutorial in the appropriate location
2. Copy all files from this template directory to your new directory
3. Edit each section file according to the guidance in the [Tutorial Template Guide](../tutorial-template-guide.md)
4. Update the links in this index file to point to your actual content files
5. Remove any sections that are not applicable to your tutorial (though most should be included)

## Example Implementation

For a complete example of this template in use, see the ["Automating Work Item Creation from External Systems"](../examples/workitem-automation-tutorial.md) tutorial.

## Template Customization

While following the standard structure, you may customize the template based on the tutorial's complexity:

- **Short Tutorials (15-30 minutes)**: Focus on core tasks, minimize advanced features
- **Complex Tutorials (30-60 minutes)**: Include detailed diagrams, more advanced features
- **Integration Tutorials**: Add sections for both systems, focus on data mapping

For detailed guidance on customizing each section, refer to the [Tutorial Template Guide](../tutorial-template-guide.md). 