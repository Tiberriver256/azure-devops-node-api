# Voice and Tone Guidelines for Azure DevOps Node API Documentation

## Overview

This document establishes the voice and tone standards for all Azure DevOps Node API documentation. Consistent voice and tone creates a cohesive experience for users and reinforces the professionalism and reliability of our API.

## General Voice Principles

### Professional but Approachable

Our documentation speaks with the voice of a helpful, knowledgeable colleague who understands both the Azure DevOps ecosystem and the challenges of Node.js development.

✅ **Do**:
- Use clear, direct language that respects the reader's intelligence
- Balance technical accuracy with accessibility
- Provide context and explanations that help developers understand not just how, but why

❌ **Don't**:
- Use overly casual language, slang, or cultural references
- Adopt an authoritative or condescending tone
- Oversimplify to the point of imprecision

### Present Tense

Use present tense to describe how the API works, as this creates immediacy and clarity.

✅ **Do**:
- "The API returns a Promise that resolves to a WorkItem object."
- "When you create a new BuildDefinition, the system validates your inputs."

❌ **Don't**:
- "The API will return a Promise that will resolve to a WorkItem object."
- "When you have created a new BuildDefinition, the system will validate your inputs."

### Active Voice

Active voice makes documentation clearer and more direct. It emphasizes who or what is performing the action.

✅ **Do**:
- "The GitClient retrieves repository information."
- "You must provide authentication credentials."

❌ **Don't**:
- "Repository information is retrieved by the GitClient."
- "Authentication credentials must be provided."

### Direct Address

Address the reader directly as "you" to create a more engaging and personal experience.

✅ **Do**:
- "You can configure the client to use different authentication methods."
- "When you implement error handling, consider using try/catch blocks."

❌ **Don't**:
- "Users can configure the client to use different authentication methods."
- "When error handling is implemented, try/catch blocks should be considered."

## Technical Voice Guidelines

### Balance Precision and Clarity

Technical documentation must be precise without becoming unnecessarily complex.

✅ **Do**:
- Use precise technical terms when they are the most accurate way to describe a concept
- Explain complex concepts with analogies or examples when appropriate
- Break down complex processes into manageable steps

❌ **Don't**:
- Use three technical terms where one would suffice
- Oversimplify technical concepts to the point of inaccuracy
- Introduce unnecessary jargon

### Define Terms on First Use

When introducing specialized terminology, provide a clear definition on first use.

✅ **Do**:
- "Resource Areas are logical groupings of API resources that share routing and versioning."
- "The Promise pattern (an asynchronous programming model where operations return a Promise object representing a future result) is used throughout the API."

❌ **Don't**:
- Use specialized terms without definition
- Define the same term repeatedly throughout a document
- Provide overly technical definitions that require additional explanation

### Context and Purpose

Always explain the why behind a feature or pattern, not just the how.

✅ **Do**:
- "The WebApi client uses factories to create service clients, which provides better separation of concerns and allows for more flexible authentication handling."
- "Batch operations reduce network traffic and improve performance when you need to create or update multiple work items."

❌ **Don't**:
- Simply describe functionality without explaining its purpose or benefits
- Assume developers understand the rationale behind API design decisions

## Content-Specific Tone Adjustments

### API Reference Documentation

- **Tone**: Precise, technical, comprehensive
- **Focus**: Accuracy, completeness, and clarity
- **Example**: "The `getRepositories` method accepts an optional `project` parameter of type `string` that filters results to a specific project. When omitted, repositories from all projects are returned."

### Getting Started Guides

- **Tone**: Helpful, encouraging, step-by-step
- **Focus**: Successful first experiences and quick wins
- **Example**: "Let's create your first connection to Azure DevOps. This simple example will retrieve your profile information to confirm everything is working correctly."

### Conceptual Guides

- **Tone**: Explanatory, educational, contextual
- **Focus**: Understanding core concepts and patterns
- **Example**: "Authentication in the Azure DevOps Node API follows a credential provider pattern. This flexible approach allows you to implement various authentication mechanisms while maintaining a consistent client interface."

### Troubleshooting Content

- **Tone**: Empathetic, solution-oriented, practical
- **Focus**: Resolving common issues quickly and effectively
- **Example**: "If you're seeing 401 Unauthorized errors, first check that your Personal Access Token includes the appropriate scopes. The most common issue is missing 'read' permissions for the resource you're trying to access."

## Brand Alignment

### Microsoft and Azure DevOps Voice

Our documentation voice should align with broader Microsoft and Azure DevOps brand guidelines while remaining specific to our developer audience.

Key principles:
- Empowering developers to achieve more
- Emphasizing enterprise-grade reliability and security
- Highlighting integration capabilities across the Microsoft ecosystem
- Focusing on productivity and efficiency

### Developer-Focused Adaptations

While maintaining brand alignment, adapt to the specific needs and expectations of Node.js developers:

- Acknowledge modern JavaScript/TypeScript development patterns and practices
- Reference related npm ecosystem tools and approaches where relevant
- Address specific challenges of JavaScript/TypeScript development in the context of Azure DevOps

## Writing Style Elements

### Contractions

Use contractions naturally to create a more conversational tone.

✅ **Do**:
- "You'll need to initialize a connection before making any API calls."
- "Don't forget to handle promise rejections."

❌ **Don't**:
- Avoid contractions entirely, which can make the tone overly formal
- Overuse contractions to the point of seeming unprofessional

### Technical Abbreviations

Use standard technical abbreviations where appropriate, but define non-standard ones.

✅ **Do**:
- Use common abbreviations like API, SDK, UI without definition
- Define less common abbreviations on first use: "Project Collection Token (PCT)"
- Be consistent with abbreviation use throughout documentation

❌ **Don't**:
- Create new abbreviations unnecessarily
- Use obscure abbreviations without explanation

### Sentence and Paragraph Length

Keep sentences and paragraphs concise for improved readability.

✅ **Do**:
- Aim for sentences of 15-20 words on average
- Limit paragraphs to 3-5 sentences
- Use bulleted or numbered lists for complex information

❌ **Don't**:
- Write sentences with multiple clauses and conditions
- Create dense paragraphs that cover multiple concepts
- Present step-by-step instructions in paragraph form

## Inclusive Language Guidelines

### Gender-Neutral Language

Use gender-neutral language throughout all documentation.

✅ **Do**:
- "The developer can access their token from the profile page."
- Use role titles: "system administrator" not "system admin guy"

❌ **Don't**:
- Use "he," "she," "his," "her" when referring to unspecified individuals
- Use gendered terms like "guys," "mankind," or "manpower"

### Accessibility in Language

Choose terms that are inclusive of people with disabilities.

✅ **Do**:
- "Select the option" rather than "Click the option"
- "Review the output" rather than "See the output"

❌ **Don't**:
- Use terms that assume specific physical abilities
- Use metaphors related to disability (e.g., "blind to the issue")

### Cultural Inclusivity

Avoid cultural-specific references or idioms that may not be understood globally.

✅ **Do**:
- Use clear, direct language that translates well
- Consider the global audience when choosing examples

❌ **Don't**:
- Use sports metaphors, cultural references, or idioms
- Make assumptions about cultural knowledge

## Implementation and Review

### Documentation Review Checklist

When reviewing documentation for voice and tone, verify that the content:

- Maintains a consistent voice throughout
- Uses active voice and present tense
- Addresses the reader directly
- Explains complex concepts clearly
- Avoids unnecessary jargon
- Uses inclusive language
- Provides context and purpose

### Balancing Consistency and Context

While consistency is important, recognize that different documentation types may require subtle adjustments in tone:

- API references may be more formal and precise
- Tutorials may be more supportive and detailed
- Troubleshooting guides may be more empathetic and solution-focused

Make these adjustments while maintaining the core voice principles.

## Examples

### Before and After Examples

**Before (Needs Improvement):**
"When utilizing the WorkItemTrackingClient, users will find that work items can be accessed and manipulated via various methods. It is advisable that proper error handling is implemented, as network connectivity issues may cause unexpected behaviors."

**After (Improved):**
"The WorkItemTrackingClient provides methods to retrieve, create, and update work items. When you use these methods, implement proper error handling to manage potential network connectivity issues."

**Before (Needs Improvement):**
"The implementation of caching mechanisms would be a recommended approach for developers looking to minimize API calls and optimize application performance."

**After (Improved):**
"Implement caching to reduce API calls and improve your application's performance. For example, cache project and team references that rarely change." 