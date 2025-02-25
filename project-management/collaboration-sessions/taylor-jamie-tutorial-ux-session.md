# Taylor & Jamie Collaboration Session: User-Centric Documentation for Tutorials

**Date:** March 15, 2023  
**Time:** 2:00 PM - 2:45 PM  
**Participants:** Taylor (Senior Technical Writer), Jamie (UI/UX Lead)  
**Topic:** Best Practices for User-Centric Documentation in Tutorials  

## Meeting Notes

### Introduction

**Taylor:** Thanks for making time to meet today, Jamie. I've been working on creating documentation templates for the Azure DevOps Node API, and I'm about to start on the tutorial template. Morgan suggested I collaborate with you to ensure we're following best practices for user-centric documentation.

**Jamie:** Happy to help! I've had a chance to review the templates you've shared. The API reference templates are very comprehensive. For tutorials, we'll want to take a slightly different approach that focuses more on user goals rather than API features.

### Key Discussion Points

#### 1. Understanding User Goals vs. API Features

**Jamie:** One of the most significant differences between reference documentation and tutorials is the organization principle. Reference docs are organized around the API structure, but tutorials should be organized around user goals.

**Taylor:** That makes sense. So instead of "Using the WorkItemTrackingApi class," we should frame it as "Managing work items in your project"?

**Jamie:** Exactly! Users don't wake up thinking, "I want to use the WorkItemTrackingApi today." They think, "I need to automate the creation of work items when a new bug is reported." Always start with the user goal, then introduce the API components needed to achieve it.

**Taylor:** I like that approach. I'll make sure the tutorial template emphasizes stating the user goal prominently at the beginning.

#### 2. Progressive Disclosure Pattern

**Jamie:** For tutorials, I recommend using a progressive disclosure pattern where you start with a simple working example, then gradually introduce complexity.

**Taylor:** So begin with a minimal viable example that accomplishes something useful, then build on it?

**Jamie:** Precisely. For instance, start with authenticating and getting a simple piece of data, show that working, then add features like filtering, error handling, etc. This gives users early wins and a sense of accomplishment.

**Taylor:** That's helpful. I'll structure the template to encourage this pattern with sections like "Basic Implementation" followed by "Adding Advanced Features."

#### 3. Visual Elements and Learning Patterns

**Jamie:** Visual elements are crucial for tutorials. Our UX research shows that users understand complex flows better when they see diagrams before code.

**Taylor:** What types of visuals do you find most effective?

**Jamie:** Three types work particularly well:
1. **Flow diagrams** showing the sequence of operations
2. **Component diagrams** showing how different parts interact
3. **Before/after screenshots** showing the outcome of the process

Also, consider using icons for different types of notes:
- 💡 for tips
- ⚠️ for warnings
- 🔍 for deeper explanations

**Taylor:** I'll incorporate these visual patterns into the template. Do you have any guidance on creating consistent diagrams?

**Jamie:** Yes, I can share our diagram style guide and Figma templates after the meeting. We've standardized on a specific color scheme and style that matches our documentation visual identity.

#### 4. Practical Accessibility Considerations

**Jamie:** Accessibility is another important aspect. Many developers may have various disabilities, so we need to ensure our documentation works for everyone.

**Taylor:** What specific accessibility features should I build into the tutorial template?

**Jamie:** Several key things:
- Always include alt text for images that describes what the image shows
- Use heading hierarchies properly (H1 > H2 > H3)
- Ensure code examples can be navigated via screen readers
- Provide text alternatives to any visual decision trees
- Use sufficient color contrast for any highlighted text
- Make sure any interactive elements are keyboard accessible

**Taylor:** I'll add accessibility requirements to the template guide. Should we have an accessibility checklist as part of the template review process?

**Jamie:** Absolutely! I can help create that checklist. It would be a great addition to your template review process.

#### 5. Learning Paths and Navigation

**Taylor:** One thing I've been thinking about is how to create connections between different types of documentation. How do we guide users from tutorials to reference docs and vice versa?

**Jamie:** This is a great question about the overall information architecture. We should create "learning paths" that connect related content:

1. At the end of each tutorial, include a "Next Steps" section that points to:
   - More advanced tutorials on related topics
   - Reference docs for components used in the tutorial
   - Conceptual guides that explain underlying principles

2. From reference docs, link to:
   - Tutorials that demonstrate practical usage
   - Related reference content

3. Use consistent terminology across all content types

**Taylor:** That addresses exactly what I was concerned about. I'll create a standardized "Next Steps" section in the tutorial template.

#### 6. Code Sample Best Practices

**Jamie:** For the code samples in tutorials, our UX research has found some interesting patterns about what works best.

**Taylor:** I'd love to hear more about that.

**Jamie:** Here are the key findings:
- Complete samples work better than fragments
- Comments should explain "why," not just "what"
- Highlight the most important lines (using bold or color)
- Show output alongside code when possible
- Indicate where error handling should go, even in basic examples
- Use realistic variable names and scenarios

**Taylor:** These are great guidelines. I'll incorporate them into the code sample section of the tutorial template.

**Jamie:** Also, we've found that users strongly prefer examples that they can run with minimal modification. If they need to replace placeholders or make changes, be explicit about exactly what they need to change.

#### 7. Troubleshooting Section

**Jamie:** Another element that users find extremely valuable in tutorials is a dedicated troubleshooting section that addresses common issues.

**Taylor:** That's a good point. What format works best for those sections?

**Jamie:** The most effective format we've found is a problem/solution approach:

```
Problem: You receive a "401 Unauthorized" error when attempting to connect.
Solution: Your personal access token may have expired. Generate a new token with the appropriate scopes (Work Items: Read & Write).
```

Users tend to scan for their specific error message, so making the problem statements clear and searchable is important.

**Taylor:** I'll add a dedicated troubleshooting section to the template with this format.

### Action Items

1. **Taylor** will:
   - Create the tutorial template incorporating these UX principles
   - Draft guidelines for visual elements in the template guide
   - Develop a "Next Steps" standard format for connecting documentation types
   - Add accessibility requirements to the template

2. **Jamie** will:
   - Share the diagram style guide and Figma templates
   - Create an accessibility checklist for documentation review
   - Provide examples of effective visual decision trees
   - Review the draft tutorial template when ready

3. **Follow-up:** Schedule a 30-minute review session once Taylor has a draft of the tutorial template ready.

### Conclusion

**Taylor:** This has been incredibly helpful, Jamie. I feel like I have a much clearer direction for creating a user-centered tutorial template now.

**Jamie:** I'm glad I could help! I'm excited to see how these UX principles will be incorporated into our documentation. It's refreshing to work with a technical writer who prioritizes the user experience.

**Taylor:** I'll share a draft of the tutorial template with you by the end of next week for your input before we finalize it.

**Jamie:** Sounds great! I've put some time on my calendar for review. Feel free to ping me if you have any questions before then. 