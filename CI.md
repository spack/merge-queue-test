# Using merge queue in spack CI

This document describes how we might use the merge queue feature in spack CI.  There are two ways we can consider taking advantage of the merge queue functionality for testing in spack, using the existing `gh-gl-sync` script, or moving to a fully event-driven system using spackbot or some other event handling system.

Currently the public beta of merge queue on GitHub has some problems:

- no api endpoints for discovering the state of the queue (order of PRs in the queue and the associated ref/sha of each)
- bugs causing stale merge queue branches to remain in the repo (for merged PRs, PRs manually removed from the queue, PRs
behind failing PRs and for which new branches were made)

## Using the `gh-gl-sync` cron job

The stateless cron job approach has some pros/cons.

### Benefits

- No risk of missing events resulting in getting out of sync or needing to catch up.

### Drawbacks

- Uses a lot of api requests unnecessarily

## Using an event-based approach

Pros/cons are pretty much opposite to those of the stateless cron approach, and as such, are not repeated here.

The events we receive from github are documented in the [NOTES.md](./NOTES.md), with links to actual example events received.

