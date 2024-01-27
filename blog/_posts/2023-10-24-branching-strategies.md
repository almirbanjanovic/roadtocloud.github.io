---
title:  "Branching Strategies"
header:
  teaser: "/assets/images/branching-strategies-decision-guide.jpg"
excerpt: "Branching strategies represent approaches used in software development to manage and organize code repositories effectively. "
toc: true
tags:
  - devops
  - code
  - sdlc
  - strategy
---

# The Big Picture
Branching strategies are approaches used in software development to manage and organize code repositories effectively. These strategies dictate how development branches are structured, merged, and deployed, providing a framework for collaboration, version control, and seamless integration in the DevOps lifecycle.  However, it is important to remember that there is no one size fits all approach. Here I discuss four (4) well-known branching strategies, which represent a good summary of standard branching strategies but mutations and customizations of them are possible and frequent. These are *Trunk-based Development, GitHub Flow, GitLab Flow, and GitFlow*.

# Trunk-based Development (Centralized Workflow)
<figure>
    <a href="/assets/images/branching-strategies-trunk.jpg"><img src="/assets/images/branching-strategies-trunk.jpg"></a>
</figure>
Trunk-based development emphasizes a single main branch with frequent small integrations to streamline collaboration. It prioritizes simplicity and continuous integration but may limit experimentation due to its singular focus. It is well-suited for smaller teams due to its simplicity and streamlined integration. However, larger teams may face challenges coordinating frequent integrations. Similarly, teams working across different geographic locations might find it easier to coordinate with a singular main branch, reducing the chances of conflicts.  This approach is best suited for a senior-level team.

## Key Points
- Simple structure with a main branch, which is always ready for production
- Commits directly to main branch
- Other short-lived branches (less than 24 hours) acceptable
- Robust CI/CD is a *must*
- Feature flags are a *must*
- Ideal for teams of eight (8) or fewer developers

## Implementation Checklist
- Educate engineers on workflow
- Confirm CI/CD is in great shape
- Confirm and validate feature flags
- Merge and delete any outstanding branches into *main* branch

## Pros
- Very simple workflow
- Production matches latest commit
- Codebase always ready for production
- Improved developer experience
- Limited or few merge conflicts

## Cons
- Strict CI/CD is a *must*
- Feature flags are a *must*
- Only really works with small teams
- Limited traceability
- Favors pair-programming over individual work


# GitHub Flow (Feature Branching)
<figure>
    <a href="/assets/images/branching-strategies-github-flow.jpg"><img src="/assets/images/branching-strategies-github-flow.jpg"></a>
</figure>
Similar to Trunk-based Development, GitHub Flow also revolves around a main branch, with developers creating feature branches, submitting pull requests, and merging changes after code review. 

## Key Points
- Long-living main branch, which is always ready for production
- Short-living feature branches
- Supports simultaneous feature development
- Strong CI/CD is a *must*
- Ideal for small to medium teams, between eight (8) and twelve (12) developers

## Implementation Checklist
- Educate engineers on workflow
- Finish outstanding work and delete other branches
- Add branch restrictions (for Pull Requests) to main branch

## Pros
- Simple workflow
- Single merge point (main branch)
- Concurrent feature development possible
- Allows for individual development and feature testing
- Provides traceability for individual features

## Cons
- Requires strong CI/CD to realize full benefit
- Basic traceability
- Merge conflicts can become difficult to resolve
- Scale is limited due to merging, pulling, deployments
- Testing *must* be automated


# GitLab Flow
<figure>
    <a href="/assets/images/branching-strategies-gitlab-flow.jpg"><img src="/assets/images/branching-strategies-gitlab-flow.jpg"></a>
</figure>
GitLab Flow expands on and shares similarities with GitHub Flow above but places additional emphasis on automated testing. It involves feature branches, merge requests, and a continuous delivery approach.

## Key Points
- Supports separate testing environment
- Multiple long-living branches such as main, testing, production
- Only short-lived branches are feature branches

## Implementation Checklist
- Educate developers on workflow and explain similarity to GitHub Flow
- Develop features in separate branches
- Utilize merge / pull requests for code reviews
- Must automate testing

## Pros
- Similar to GitHub Flow
- Integrates testing for higher code quality

## Cons
- Testing must be fully automated, which might present challenges to some teams
- Limited traceability


# Git Flow
<figure>
    <a href="/assets/images/branching-strategies-git-flow.jpg"><img src="/assets/images/branching-strategies-git-flow.jpg"></a>
</figure>
This is the most complex workflow, which involves multiple short-living and long-living branches.  However, Git Flow introduces a more structured branching model with designated branches for features, releases, and hotfixes. It offers a comprehensive strategy for projects with complex release cycles.

## Key Points
- Good for grouping multiple features into specific releases
- Multiple short-living branches such as feature, release, hotfix
- Multiple long-living branches such as main and develop
- Well-established process for hot fixes
- Ideal for large teams of ten (10) or more developers in multiple georgraphic locations

## Implementation Checklist
- Determine and add branch protections (pull requests, permissions)
- Merge and delete any outstanding branches
- Add gating for approvals and testing
- Pick main and develop branches

## Pros
- Great for specific testing and compliance requirements
- Supports managing multiple versions
- Great traceability between features and branches
- Works well with large teams with sub-teams across different time zones
- Can bundle multiple features in one release

## Cons
- Very complex process
- Very high learning curve
- May require dedicated release manager
- Merge conflicts are frequent and complicated
- Merge conflicts require line-by-line resolution
- Difficult and long path to production

# Decision Guide and Summary
In conclusion, trunk-based development, GitHub Flow, GitLab Flow, and GitFlow provide different approaches to branching, catering to varying project needs, team sizes and seniority. Trunk-based and GitHub/GitLab Flow are simpler and favor continuous integration, while GitFlow introduces more structure for traceability for projects with complex release strategies. The choice depends on the project's size, complexity, and the desired balance between simplicity and structure.

<figure>
	<a href="/assets/images/branching-strategies-decision-guide.jpg"><img src="/assets/images/branching-strategies-decision-guide.jpg"></a>
</figure>
