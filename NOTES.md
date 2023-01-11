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

Used branch settings to choose force checks to pass before merging.  No checks had
been found, so I was unable to select any specifically.

Github reminded me checks would not be enforced until I upgraded the private repo.

### Created a second PR

After creating second PR (now status checks must pass, but also github said this
would not be enforced), the second PR has not been merged immediately.

Fetching from the repo, there is now another new branch:

```
gh-readonly-queue/main/pr-2-ffa88c5e89dc38200a2bd4459e74bfa12c09641d
```

It took longer to merge this time, but the second PR was eventually (after 5-10 min)
merged without any check status reported.
