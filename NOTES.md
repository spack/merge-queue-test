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

Immediately after that, we seem to get a `pull_request` event, with the action `enqueued` ([example](events/pull_request_enqueued.json)).  Looking at that webhook in the [docs](https://docs.github.com/developers/webhooks-and-events/webhooks/webhook-events-and-payloads#pull_request), you can find `dequeued` as one of the action types, but not `enqueued`.

After some delay, we get three more events without any more action on our part, corresponding to the PR getting merged:

1. event type: `pull_request`, action: `dequeued` ([example](events/pull_request_dequeued.json))
2. event type: `pull_request`, action: `closed` ([example](events/pull_request_closed.json))
3. event type: `merged_queue`, action: `deleted` ([example](events/merge_queue_deleted.json))


### Fourth and fifth PRs

When I created two PRs and added them both to the merge queue (by clicking "Merge when ready" on each one) at nearly the same time, the second one got put in the queue, and github said there was one PR ahead of it.

#### Branch activities

I fetched from the repo after adding both open PRs to the merge queue (nearly simultaneously), and saw two new branches:

```
upstream/gh-readonly-queue/main/pr-4-745f3d1210f30123cc29b857e19ba2677c7484a2
upstream/gh-readonly-queue/main/pr-5-529fb1ecc0fea2dcc27d0df8d82f40ff2c62680a
```

The git log of those branches show that the one created second includes the changes (and in fact the exact merge commit) present in the one created first.

Here's the git log of the branch corresponding to the *first* PR in the queue:


```
$ git log -n 4 upstream/gh-readonly-queue/main/pr-5-529fb1ecc0fea2dcc27d0df8d82f40ff2c62680a
commit 745f3d1210f30123cc29b857e19ba2677c7484a2 (upstream/gh-readonly-queue/main/pr-5-529fb1ecc0fea2dcc27d0df8d82f40ff2c62680a)
Merge: 529fb1e 44099ba
Author: Scott Wittenburg <scott.wittenburg@kitware.com>
Date:   Wed Jan 11 20:31:56 2023 +0000

    Merge pull request #5 from spack/add-event-examples

    Add actual event payloads

commit 44099ba1a1bcc36984a85728891ef22d1ff26ddd (HEAD -> add-event-examples, upstream/add-event-examples)
Author: Scott Wittenburg <scott.wittenburg@kitware.com>
Date:   Wed Jan 11 13:29:08 2023 -0700

    Add actual event payloads

commit 529fb1ecc0fea2dcc27d0df8d82f40ff2c62680a (upstream/main, upstream/HEAD, main)
Merge: 317333f 9cd8783
Author: Scott Wittenburg <scott.wittenburg@kitware.com>
Date:   Wed Jan 11 18:40:37 2023 +0000

    Merge pull request #3 from spack/add-notes-md

    move notes to another file
```

And here's the git log of the branch corresponding to the *second* PR in the queue:


```
$ git log upstream/gh-readonly-queue/main/pr-4-745f3d1210f30123cc29b857e19ba2677c7484a2
commit 1b1aaaec8784d524335dc225a4e21030941424a5 (upstream/gh-readonly-queue/main/pr-4-745f3d1210f30123cc29b857e19ba2677c7484a2)
Merge: 745f3d1 67296f1
Author: Scott Wittenburg <scott.wittenburg@kitware.com>
Date:   Wed Jan 11 20:32:02 2023 +0000

    Merge pull request #4 from spack/describe-event-flow

    Describe merge queue event flow with a single PR

commit 745f3d1210f30123cc29b857e19ba2677c7484a2 (upstream/gh-readonly-queue/main/pr-5-529fb1ecc0fea2dcc27d0df8d82f40ff2c62680a)
Merge: 529fb1e 44099ba
Author: Scott Wittenburg <scott.wittenburg@kitware.com>
Date:   Wed Jan 11 20:31:56 2023 +0000

    Merge pull request #5 from spack/add-event-examples

    Add actual event payloads

commit 44099ba1a1bcc36984a85728891ef22d1ff26ddd (HEAD -> add-event-examples, upstream/add-event-examples)
Author: Scott Wittenburg <scott.wittenburg@kitware.com>
Date:   Wed Jan 11 13:29:08 2023 -0700

    Add actual event payloads

commit 67296f1a63e3a6d31effdbb2571e018b5536c39a (upstream/describe-event-flow, describe-event-flow)
Author: Scott Wittenburg <scott.wittenburg@kitware.com>
Date:   Wed Jan 11 13:28:16 2023 -0700

    Describe merge queue event flow with a single PR

commit 529fb1ecc0fea2dcc27d0df8d82f40ff2c62680a (upstream/main, upstream/HEAD, main)
Merge: 317333f 9cd8783
Author: Scott Wittenburg <scott.wittenburg@kitware.com>
Date:   Wed Jan 11 18:40:37 2023 +0000

    Merge pull request #3 from spack/add-notes-md

    move notes to another file
```
