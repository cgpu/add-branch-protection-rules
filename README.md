# Programmatically add branch protection rules to a repo

## Prerequisites

1. Linux or Mac machine
2. A github token with the required scopes for updating self or org repos.
3. (Optional) Docker installed, if using the container

### Step 0: Create a Github authentication token

To create the token for both org and self repo administration you can click on the quick link below:
https://github.com/settings/tokens/new?scopes=repo,user,admin:org&description=admin-self-org-repos


## Usage

The [Github cli `gh`](https://github.com/cli/cli) is required. You can [follow the recommended installation](https://cli.github.com/manual/installation) or use the container `quay.io/lifebitai/gh` that includes the library.

If you want to use the container interactively you can make use of the command:

```bash
docker run --rm -it quay.io/lifebitai/gh
```

**NOTE**: For the remaining instructions it is assumed you are using this option (Docker), and you are executing all following commands in the container 

### Step 1: Authenticate with the Github token via cli 

Authenticate using the token created at [`step 0`](https://github.com/lifebit-ai/add-branch-protection-rules#step-0-create-a-github-authentication-token)

```bash
# inside the container quay.io/lifebitai/gh
gh auth login
```

![](http://g.recordit.co/cCeUnno37h.gif)

Follow the interactive prompt and select the following options in the choice menu:

```console
? What account do you want to log into? GitHub.com
? What is your preferred protocol for Git operations? HTTPS
? Authenticate Git with your GitHub credentials? No
? How would you like to authenticate GitHub CLI? Paste an authentication token
```

At the last prompt paste the authentication token.

You should see a similar message in your stdout if the step was completed successfully.

```console
? Paste your authentication token: ****************************************
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as << gh-username-used-to-generate-the-token >>
```

### Step 2: Use `createBranchProtectionRule.sh` to apply branch protection rules to 1 repo

```bash
# continue inside the container
git clone https://github.com/lifebit-ai/add-branch-protection-rules
cd add-branch-protection-rules

# use createBranchProtectionRule.sh
ORG_NAME="lifebit-ai"
REPO_NAME="new-repo-that-does-not-already-have-branch-protection-in-place"
BRANCH_PATTERN="main"

./createBranchProtectionRule.sh \
github.com  \
$ORG_NAME \
$REPO_NAME  \
$BRANCH_PATTERN \
requiresApprovingReviews=true \
requiresCodeOwnerReviews=true \
requiredApprovingReviewCount=1 \
requiresStatusChecks=true \
requiresStrictStatusChecks=false \
requiresLinearHistory=true
```

If the step has been completed successfully, you should see something similar to the message below:

```json
{
  "data": {
    "createBranchProtectionRule": {
      "branchProtectionRule": {
        "allowsDeletions": false,
        "allowsForcePushes": false,
        "creator": {
          "login": $ORG_NAME
        },
        "databaseId": 22404224,
        "dismissesStaleReviews": false,
        "isAdminEnforced": false,
        "pattern": $BRANCH_PATTERN,
        "repository": {
          "nameWithOwner": "$ORG_NAME/$REPO_NAME"
        },
        "requiredApprovingReviewCount": 1,
        "requiresApprovingReviews": true,
        "requiredStatusCheckContexts": [
        ],
        "requiresCodeOwnerReviews": true,
        "requiresCommitSignatures": false,
        "requiresLinearHistory": true,
        "requiresStatusChecks": true,
        "requiresStrictStatusChecks": false,
        "restrictsPushes": false,
        "restrictsReviewDismissals": false
      },
      "clientMutationId": null
    }
  }
}
```

```console
SUCCESS: Branch protection rule successfully created
```

### Step 3: Verify that the branch protection rules have been applied

Navigate to https://github.com/$ORG_NAME/$REPO_NAME/settings/branches and inspect if under the `Branch protection rules` section you can see the branch $BRANCH_NAME that you just modified with the script above. Click `edit` to be able to review if the rule checkboxes have been marked.

You should see sth like this:

![](http://g.recordit.co/NIDEIGpbpB.gif)




