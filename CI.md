# Using merge queue in spack CI

This document describes how we might use the merge queue feature in spack CI.  There are two ways we can consider taking advantage of the merge queue functionality for testing in spack, using the existing `gh-gl-sync` script, or moving to a fully event-driven system using spackbot or some other event handling system.

## Using the `gh-gl-sync` cron job



## Using an event-based approach

The events we receive from github are documented in the [NOTES.md](./NOTES.md), with links to actual example events received.

