---
name: deployment-assistant
description: "Guide deployments to staging or production with step-by-step walkthroughs, verification checkpoints, and rollback instructions. Use this skill whenever the user needs to deploy code, push to staging, ship to production, handle CI/CD pipelines, or troubleshoot a failed deployment. Trigger on phrases like 'deploy this,' 'push to staging,' 'ship to prod,' 'deployment failed,' 'rollback,' 'CI/CD pipeline,' or any request involving getting code from a branch to a running environment."
---

# Deployment Assistant

## Purpose
Deployment is anxiety-inducing. The user needs guided, procedural support — not generative logic. This skill walks through deployments step-by-step with verification at each checkpoint.

## Dependencies
- **Code Writer (Skill 2):** Repo context and environment awareness.
- **GitHub API:** Push, branch management.
- **CI/CD pipeline access.**

## Inputs
- Target environment (staging, production, etc.)
- Repo context (branch, recent commits)
- CI/CD pipeline profile (cached after first ingestion)

## Outputs
- Step-by-step deployment walkthrough
- Verification checkpoint at each step
- Blocker flags when something looks wrong
- Rollback instructions if things go sideways

## Workflow

### Pre-Deployment
1. Load only the **target environment** config — not all environments
2. Check CI/CD pipeline profile (cached) for known triggers, stages, and typical failure points
3. Verify branch is clean, tests passing, and ready to deploy
4. Present the deployment plan: numbered steps with estimated time

### Deployment Execution
Walk through each step one at a time:
1. Present the step
2. Execute or instruct the user to execute
3. Verify completion (check status, logs, health endpoints)
4. Flag any blockers before proceeding
5. Move to next step only after verification passes

Typical steps include:
- Git push to deployment branch
- CI/CD pipeline trigger and monitoring
- Environment variable verification
- Database migration status
- Health check endpoints
- Smoke test execution

### Error Handling
**Reactive, not preloaded.** Don't anticipate every possible failure. When something breaks:
1. Read the error logs
2. Diagnose the specific issue
3. Recommend fix or rollback
4. If rollback needed, provide exact steps

### Rollback Procedure
Always have rollback steps ready:
- How to revert the deploy
- How to restore the previous version
- How to verify the rollback succeeded
- Post-rollback health checks

## Deployment Checklist Templates
Store per-environment checklist templates. These are mostly procedural — the same steps each time with environment-specific details filled in. Update templates when the deployment process changes.

## Context Rules
- **Load only target environment** config, not all environments
- **CI/CD pipeline profile cached** — don't re-read pipeline configs each deployment
- **Error handling is reactive** — load error logs only when a failure occurs
- **Checklist templates** stored as reference files, loaded per environment
