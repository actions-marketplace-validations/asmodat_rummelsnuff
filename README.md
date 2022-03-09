Rummelsnuff - but works for all PR's not just external forks
===========

A GitHub action to mark and close spam PRs created to get a free HacktoberFest T-shirt.

Rules
-----

A pull request is considered as spam if it's coming from a forked repository and meets at least one of following criterias:

* The author has registered after this year's Hacktoberfest and has only forked repositories
* The PR is changing documentation insignificantly
* The PR consists of additions or deletions in a single file only

Configuration
-------------

The action needs an access token to manage PRs. To provide an access token, add `access_token: ${{ secrets.GITHUB_TOKEN }}` to the `with:` section of your workflow step (see example below).

By default this action adds "Spam" label and closes the PR that is recognized as spam. A custom label can be provided via the `spam_label` input. To disable closing PRs set the `close_spam_prs` to any value except `"yes"`, for example:

``` yaml
env:
      REF_BRANCH: ${{ github.event.pull_request.head.ref }}
      BASE_REF_BRANCH: ${{ github.base_ref }}
 steps:
  job1:
    - name: Extract branch name on pull request
            shell: bash
            run: |
              echo "SOURCE_BRANCH=$(echo ${{ env.REF_BRANCH }} | tr / -)" >> $GITHUB_ENV
              echo "DESTINATION_BRANCH=$(echo ${{ env.BASE_REF_BRANCH }} | tr / -)" >> $GITHUB_ENV
          - name: Print debug data before publishing
            run: |
              echo "Source branch name: ${{ env.SOURCE_BRANCH }}"
              echo "Destination branch name: ${{ env.DESTINATION_BRANCH }}"
  - name: Rummelsnuff
     uses: asmodat/rummelsnuff@v1.1.1
     if: |
         ( startsWith(env.DESTINATION_BRANCH, 'v') && contains(env.DESTINATION_BRANCH, '.') ) &&
         ( !startsWith(env.SOURCE_BRANCH, 'feature') && !startsWith(env.SOURCE_BRANCH, 'bugfix') )
       with:
          spam_label: "Invalid Target"
          close_spam_prs: "yes"
          access_token: ${{ secrets.GITHUB_TOKEN }} 
```
