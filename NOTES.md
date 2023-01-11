## Notes

Noting interactions and what happens

### Created first PR

Created a first PR, it went into a merge queue and was merged almost immediately.

Fetching from the repo later, there is a new branch:

```
gh-readonly-queue/main/pr-1-ffa88c5e89dc38200a2bd4459e74bfa12c09641d
```

That points to the same merge commit as `main`.

### Required status checks to pass

Used branch settings to choose force checks to pass before merging.  No checks had been found, so we were unable to select any specifically.

Github reminded that checks would not be enforced until we upgraded the private repo.

### Created a second PR

After creating second PR (now status checks must pass, but also github said this would not be enforced), the second PR has not been merged immediately.

Fetching from the repo, there is now another new branch:

```
gh-readonly-queue/main/pr-2-ffa88c5e89dc38200a2bd4459e74bfa12c09641d
```

It took longer to merge this time, but the second PR was eventually (after 5-10 min) merged without any check status reported.

### Third PR

Commented all the other spackbot functionality out, to just print the event data we're getting, and redeployed spackbotdev.

#### When we click "Merge when ready"

We get a `merged_queue` event (as opposed to what the [docs](https://docs.github.com/en/developers/webhooks-and-events/webhooks/webhook-events-and-payloads#merge_group) say, `merge_group`), where the action is `created` ([example](events/merge_queue_created.json)).

Immediately after that, we seem to get a `pull_request` event, with the action `enqueued`.  Looking at that webhook in the [docs](https://docs.github.com/developers/webhooks-and-events/webhooks/webhook-events-and-payloads#pull_request), you can find `dequeued` as one of the action types, but not `enqueued`.

After some delay, we get three more events without any more action on our part, corresponding to the PR getting merged:

1. event type: `pull_request`, action: `dequeued`
2. event type: `pull_request`, action: `closed`
3. event type: `merged_queue`, action: `deleted`
