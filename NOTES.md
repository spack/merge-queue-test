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

### With repo public and specific check required

Things behaved differently once the repo was made public (github would enforce the branch protections) and once spackbot-test had posted status on a commit.  With the posted status, we can choose it in the branch protection settings as a required status, and then github waits for that status (using yellow color to indicate pending status, "merge when ready" button is black, not green, though it seems I could still click it).

When these two things are set, we started getting a new event from github (`merge_group / checks_requested`, see [example](./events/merge_group_checks_requested.json)).

Also, at this point, we enabled a new PR comment message:

```
@spackbot-test check <success|failure> <sha>
```

which allows to pass or fail any commit at a time of our choosing.  This is convenient to make sure we had time to inspect the state of the repo by fetching branches and looking at logs, examing the github ui, etc.  We can also choose to pass/fail items in the queue in a specific order to see how github responds.  Here is an experiment we conducted once we had the capabilities outlined in this section.

With everything above in place, I posted a success status on the latest commit of an open PR, which turned the "merge when ready" button green, so I pushed it and confirmed.

- This triggered a new type of event from github, as mentioned above, a `merge_group` event with action set to `checks_requested`.
    _ the event object had a key `merge_group` with fields including `base_sha` as described in this [doc](https://github.blog/changelog/2022-08-18-merge-group-webhook-event-and-github-actions-workflow-trigger).  This `base_sha` was `fe863b8ab7a820b363bfdccf002ae1dc7925a598`.

- Fetching from the repo at this point resulted in a new branch `gh-readonly-queue/main/pr-6-1b1aaaec8784d524335dc225a4e21030941424a5`:

    $ git log -n 5 upstream/gh-readonly-queue/main/pr-6-1b1aaaec8784d524335dc225a4e21030941424a5
    commit fe863b8ab7a820b363bfdccf002ae1dc7925a598 (upstream/gh-readonly-queue/main/pr-6-1b1aaaec8784d524335dc225a4e21030941424a5)
    Merge: 1b1aaae 00cc0e8
    Author: Scott Wittenburg <scott.wittenburg@kitware.com>
    Date:   Thu Jan 12 16:45:20 2023 +0000

        Merge pull request #6 from scottwittenburg/add-ci-proposal-doc

        Add a CI document describing how we can use merge queue

- Here is the order of events received after clicking "Merge when ready":

    - merge_queue_entry / created
    - pull_request / enqueued
    - merge_group / checks_requested

- Once I asked @spackbot-test to post succes on `fe863b8ab7a820b363bfdccf002ae1dc7925a598`, the PR was automatically merged.  This corresponded with receiving the following events:

    - pull_request / dequeued
    - pull_request / closed
    - merge_queue_entry / deleted

### Experimenting with several PRs in the queue

For this experiment, we create three PRs (#7, #8, and #9) with passed tests (thanks to @spackbot-test check success <sha>) ready to add to merge queue.  Because we had set the merge group size to `small` (corresponding to 2 PRs per group), we didn't expect to be able to add all three PRs to the merge queue, but it was allowed anyway.

The three PRs in were in the queue in the following order, from highest/first to lowest/last:

1. PR #7 (GH branch included only #7 merged into base)
2. PR #8 (GH branch included the merge commit from (1), above, as its base)
3. PR #9 (GH branch included the merge commit from (2), above, as its base, so it included changes from #7 and #8)

First test was to ask @spackbot-test to fail the middle one in the queue (2), and after 5-10 min, nothing had been removed from the queue.  While that is not what this [doc](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue) says will happend, it may just be the doc ommitted to mention action is only taken when status is reported on the first item in the queue.

For the next test, we posted success on the third PR in the queue, and github seemed to do nothing with that information.  But some details to note:

    - the success status is not even reflected on the merge queue page like the failure status is for the middle one
    - if you go the branches UI, however, you can see the green check on that one

So next we posted failure on the first PR in the queue, and this caused github to take the following actions:

    - PR 7 was indeed removed from the merge queue
    - the merge queue contains only PRs 8 and 9 (8 is ahead, so 9 includes 8, as expected)
    - the old branches for 7,8 and 7,8,9 are still in the branches page with their status
    - the old branch for just 7 is deleted
    - there are new branches listed for 8 and 8,9
        - note these are not the same branch names, just force-pushed, as the branch name includes the base sha
        - e.g. before, the branch for 7 had base sha "sha1" and the branch for 7,8 assumed a base sha w/ 7 having been merged, so when 7 is remove, the new branch for just 8 has base sha "sha1"
        - this means just pushing the new branches to gitlab won't cancel and previous pipelines
    - received three events from github:
        - `merge_queue_entry` / `deleted`
        - `merge_group` / `checks_requested` (`merge_group` -> `head_sha` = `ed904b3e30246aeca6c3745607632c6272e94bd2`)
            - corresponds to the new branch for just 8
        - `merge_group` / `checks_requested` (`merge_group` -> `head_sha` = `a34c222dbcfff335ebfe4845300221b517853d72`)
            - corresponds to the new branch for 8,9

To complete this experiment we had @spackbot-test pass both of the new merge_group checks.  First we passed the check for PR 9 (which included the changes from both 8,9).  This didn't cause github to react in any way, and after nothing happened for awhile, we passed the check for just 8 (the first PR in the merge queue).  This caused the following things to happen:

    - the merge queue became empty, with both PRs (8 and 9) getting merged
    - both of the of the (new) readonly merge queue branches had been deleted
    - received the expected github events associated with merging a PR (mentioned above)

One thing to note is that after this experiment, the branches associated with PRs that had to be re-added to the queue when something ahead of them failed seem to just be orphaned.  They're still sitting on github right now.

## Testing with "squash and merge" approach

Repeating some of the experiments above, but now with a different merge strategy.
