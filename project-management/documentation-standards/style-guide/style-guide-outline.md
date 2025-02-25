# Azure DevOps Node API Documentation Style Guide

## Outline

This document outlines the standards and conventions for all Azure DevOps Node API documentation. Following these guidelines will ensure consistency, clarity, and usability across all documentation components.

## 1. Voice and Tone

### 1.1 General Principles
- Use a professional but approachable tone
- Write in present tense
- Use active voice (not passive)
- Address the reader directly using "you"
- Avoid jargon and overly complex language
- Be concise but complete

### 1.2 Technical Voice Guidelines
- Maintain technical accuracy while being accessible
- Explain complex concepts clearly
- Define technical terms on first use
- Use consistent terminology throughout

### 1.3 Brand Voice
- Align with Microsoft and Azure DevOps brand guidelines
- Focus on developer empowerment and productivity
- Highlight integration capabilities
- Emphasize enterprise-grade reliability and security

## 2. Formatting and Structure

### 2.1 Document Organization
- Use consistent hierarchical headings (H1 > H2 > H3, etc.)
- Include a table of contents for longer documents
- Group related content logically
- Use clear, descriptive headings

### 2.2 Text Formatting
- Use bold for UI elements and emphasis
- Use italics for introducing terms and slight emphasis
- Use monospace font for code, properties, methods, and other code elements
- Use sentence case for headings
- Keep paragraphs short (3-5 sentences)

### 2.3 Lists and Tables
- Use bulleted lists for unordered items
- Use numbered lists for sequential steps
- Use tables for structured data and comparisons
- Include headers for all tables
- Keep table content concise

### 2.4 Callouts and Notes
- Use consistent formatting for notes, warnings, and tips
- Distinguish between different types of callouts visually
- Keep callouts brief and relevant
- Use appropriate icons for each callout type

## 3. Code Examples

### 3.1 General Standards
- Ensure all code examples can be run without modification
- Include complete, working examples
- Follow TypeScript/JavaScript best practices
- Use consistent coding style
- Include comments for complex operations

### 3.2 Inline Code
- Use backticks for inline code
- Format as `ClassName.methodName()` for method references
- Format as `propertyName` for property references
- Include type information where helpful: `value: string`

### 3.3 Code Blocks
- Use fenced code blocks with language specification
- Include output where applicable
- Follow consistent indentation (2 spaces)
- Highlight important lines where necessary
- Use placeholders for variable values that users will replace

### 3.4 Error Handling
- Include error handling in all examples
- Show common error scenarios and their resolution
- Use try/catch blocks where appropriate
- Demonstrate proper promise/async-await error handling

## 4. Terminology

### 4.1 Glossary Standards
- Define all domain-specific terms
- Maintain consistency with Azure DevOps terminology
- Align with TypeScript/JavaScript ecosystem terminology
- Include acronyms and abbreviations with full forms

### 4.2 Standard Verbs
- Consistent verbs for actions (e.g., "create" not "add" or "build")
- Use "retrieve" or "get" for read operations consistently
- Use "update" for modification operations
- Use "delete" or "remove" consistently

### 4.3 API-Specific Terms
- Standardize descriptions of API objects and entities
- Consistent naming for parameters and return values
- Use approved Azure DevOps entity names
- Maintain consistency with REST API terminology

## 5. Content Types

### 5.1 API Reference Documentation
- Consistent structure for class, method, and property documentation
- Standard format for parameters and return values
- Include type information and constraints
- Link to related methods and examples

### 5.2 Conceptual Guides
- Include clear explanations of core concepts
- Use diagrams for complex relationships
- Connect concepts to practical applications
- Include "learn more" links to detailed resources

### 5.3 Tutorials
- Step-by-step format with clear objectives
- Include prerequisites and setup requirements
- Use progressive examples from simple to complex
- Include troubleshooting guidance

### 5.4 Code Samples
- Organized by scenario or API area
- Include complete working examples with comments
- Demonstrate best practices in implementation
- Include sample output or screenshots where applicable

## 6. Linking and Cross-Referencing

### 6.1 Internal Links
- Use relative links for internal documentation
- Link to relevant API references from tutorials
- Link between related concepts
- Use descriptive link text (not "click here")

### 6.2 External Links
- Link to official Microsoft and Azure DevOps resources
- Link to TypeScript/Node.js documentation for standard concepts
- Indicate when links lead to external sites
- Regularly verify external links

### 6.3 See Also Sections
- Include related content links at the end of documents
- Group related links by category or purpose
- Include brief descriptions of linked content
- Limit to 3-5 most relevant links

## 7. Versioning and Updates

### 7.1 Version Indicators
- Clearly mark version-specific content
- Note deprecated features and upcoming changes
- Include version compatibility information
- Use consistent version notation

### 7.2 Change Tracking
- Document significant changes between versions
- Highlight breaking changes prominently
- Include migration guidance for breaking changes
- Maintain a change log for documentation updates

## 8. Accessibility and Inclusivity

### 8.1 Accessible Content
- Write clear, concise sentences
- Use proper heading hierarchy
- Provide alt text for all images
- Use descriptive link text

### 8.2 Inclusive Language
- Use gender-neutral language
- Avoid cultural-specific references
- Follow Microsoft's inclusive language guidelines
- Avoid unnecessarily technical or exclusive terminology

## 9. Images and Diagrams

### 9.1 General Standards
- Use high-quality, crisp images
- Include alt text for all images
- Use a consistent visual style
- Caption complex diagrams

### 9.2 Diagram Standards
- Create clear architecture and flow diagrams
- Use consistent shapes and colors for meaning
- Include legends for complex diagrams
- Ensure diagrams are readable at different scales

## 10. Implementation Guidelines

### 10.1 File Organization
- Consistent file naming conventions
- Logical folder structure
- README files in each directory
- Index files for navigation

### 10.2 Markdown Standards
- Use GitHub Flavored Markdown
- Consistent heading structure
- Standard formatting for code, tables, and callouts
- Limit line length for improved readability

### 10.3 Review Process
- Technical accuracy review
- Editorial style review
- Consistency check
- User experience testing 