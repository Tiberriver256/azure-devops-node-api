# Documentation Standards Proposals

This directory contains proposals for documentation standards, templates, and approaches for the Azure DevOps Node API documentation.

## Directory Structure

- **draft/**: Initial proposal drafts before formal review
- **review/**: Proposals currently under review by stakeholders
- **accepted/**: Approved proposals that will be implemented
- **rejected/**: Proposals that were not approved, with reasons for rejection

## Proposal Process

1. **Draft**: Author creates initial proposal in the draft directory
2. **Review**: Proposal is moved to review directory and shared with stakeholders
3. **Decision**: Based on feedback, proposal is either accepted or rejected
4. **Implementation**: Accepted proposals are implemented according to the specifications

## Current Accepted Proposals

- [Expandable API Reference Proposal](./accepted/expandable-api-reference-proposal.md): Approach for enhancing complex API reference documentation
- [Split View GitHub Approach](./accepted/split-view-github-approach.md): Implementation of the expandable view concept within GitHub constraints
- [Expandable View Update](./accepted/expandable-view-update.md): Adaptation of the original proposal to work with GitHub limitations

## Current Rejected Proposals

- [Expandable View Mockup](./rejected/expandable-view-mockup.md): Original mockup for client-side toggle implementation (rejected due to budget constraints)

## Proposal Template

New proposals should follow this basic structure:

```markdown
# Proposal: [Title]

**Date:** [Date]  
**Author:** [Name and Role]  
**Subject:** [Brief description]  
**Status:** DRAFT | REVIEW | ACCEPTED | REJECTED

## Executive Summary

[Brief overview of the proposal]

## Background

[Context and motivation for the proposal]

## Proposed Solution

[Detailed description of the proposed approach]

## Benefits

[Expected benefits and improvements]

## Considerations

[Potential challenges and mitigations]

## Implementation Plan

[Steps for implementing the proposal]
```

## Related Resources

- [Documentation Standards](../README.md): Overview of documentation standards
- [Templates](../templates/): Documentation templates
- [Examples](../examples/): Example implementations 