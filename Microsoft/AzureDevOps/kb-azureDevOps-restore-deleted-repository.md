# Restore a deleted repository from Azure DevOps

## Introduction

The fact that you are reading this probably means you or someone else deleted an Azure Repo that shouldn't have been removed.
Well, fortunately, Azure DevOps does have a recycle bin where you can restore repositories from for 30 days.
The problem is, that you can't restore it from the Azure DevOps web interface. This page will give you instructions how to query and restore your Azure Repo.

## Prerequisites

You can use either Curl or Postman. In this article, we will use Curl, as this is installed by default on Linux and current builds of Windows 11.
You also need to create a Personal Access Token (PAT) to query and restore the repository using either Curl or Postman.

## Restore procedure

### Create PAT token

- Go to `https://dev.azure.com/{your-org}/_usersSettings/tokens`
- Create a new token, and under Code permissions, select Full.
- Keep the expiry short (e.g., 1 day) for security.
- Copy and store the generated secret token to someplace safe.

### Query deleted repositories with Curl

- Open PowerShell (I use PowerShell 7) or your preferred terminal or use Bash on Linux.
- Use the following command to query deleted repositories:

    ```bash
    curl -u :<token> "https://dev.azure.com/<orgName>/<projectName>/_apis/git/deletedrepositories?api-version=7.1"
    ```

- In the above command replace the following and make sure you remove the chevrons (`<` `>`) surrounding them:
  - `<token>`       - Your PAT your created in the previous step
  - `<orgName>`     - The Azure DevOps organization name.
  - `<projectName>` - The Azure DevOps project that contained the deleted repo.

When done correctly, you should receive output in JSON containing the removed repositories.
Do yourself a favor and copy the output to VSCode and format it to JSON and then use the format document command the make it more readable.

For each deleted repository, you should get something like this:

```json
        {
            "id": "11111111-2222-3333-4444-555555555555",
            "name": "mySuperImportantRepo",
            "project": {
                "id": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
                "name": "ProjectX",
                "description": "Project X is a dummy project name",
                "url": "https://dev.azure.com/contoso/_apis/projects/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
                "state": "wellFormed",
                "revision": 123,
                "visibility": "private",
                "lastUpdateTime": "2023-01-06T09:37:12.067Z"
            },
            "deletedBy": {
                "displayName": "JohnDoe",
                "url": "https://spsprodneu1.vssps.visualstudio.com/00000000-1111-2222-3333-444444444444/_apis/Identities/99999999-8888-7777-6666-555555555555",
                "_links": {
                    "avatar": {
                        "href": "https://dev.azure.com/contoso/_apis/GraphProfile/MemberAvatars/aad.fakeavatarid"
                    }
                },
                "id": "99999999-8888-7777-6666-555555555555",
                "uniqueName": "johndoe@contoso.com",
                "imageUrl": "https://dev.azure.com/contoso/_api/_common/identityImage?id=99999999-8888-7777-6666-555555555555",
                "descriptor": "aad.fakeavatarid"
            },
            "createdDate": "2024-02-11T09:19:10.81Z",
            "deletedDate": "2026-02-21T16:23:21.76Z"
        },
```

The first `"id"` is the va;ie you need for the restore.

> **TIP:** If for some reason the command does not work, make sure you have curl installed and there are no spaces or line breaks in your command and the URI and token are not encapsulated with any chevrons anymore.

### Restore the repository

Now that we have the id from the previous step, we can restore the repo with this curl PATCH command:

```bash
curl -u :<token> -X PATCH -H "Content-Type: application/json" -d '{"deleted": false}' "https://dev.azure.com/<orgName>/<projectName>/_apis/git//recycleBin/repositories/<repositoryId>?api-version=7.1"
```

- In the above command replace the following and make sure you remove the chevrons (`<` `>`) surrounding them:
  - `<token>`       - Your PAT your created in the previous step
  - `<orgName>`     - The Azure DevOps organization name.
  - `<projectName>` - The Azure DevOps project that contained the deleted repo.
  - `<repositoryId>`- The id of the deleted repository noted from the previous step.

The real command would look something like this:

```bash
curl -u :yourPATtoken1234567890fakevalue -X PATCH -H "Content-Type: application/json" -d '{"deleted": false}' "https://dev.azure.com/contoso/projectX/_apis/git//recycleBin/repositories/11111111-2222-3333-4444-555555555555?api-version=7.1"
```

> **TIP:** If for some reason the command does not work, make sure you have curl installed and there are no spaces or line breaks in your command and the URI and token are not encapsulated with any chevrons anymore.
>
> Also, please note that the URI is different then that of the pervious step.

If all went well, you should get a JSON response again that looks like this:

```json
{
    "id": "11111111-2222-3333-4444-555555555555",
    "name": "mySuperImportantRepo",
    "url": "https://dev.azure.com/ictstuff/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/_apis/git/repositories/11111111-2222-3333-4444-555555555555",
    "project": {
        "id": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
        "name": "ProjectX",
        "description": "Project X is a dummy project name",
        "url": "https://dev.azure.com/ictstuff/_apis/projects/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
        "state": "wellFormed",
        "revision": 123,
        "visibility": "private",
        "lastUpdateTime": "2025-07-06T09:37:12.067Z"
    },
    "defaultBranch": "refs/heads/main",
    "size": 27692291,
    "remoteUrl": "https://contoso@dev.azure.com/contoso/projectX/_git/mySuperImportantRepo",
    "sshUrl": "git@ssh.dev.azure.com:v3/contoso/projectX/mySuperImportantRepo",
    "webUrl": "https://dev.azure.com/contoso/projectX/_git/mySuperImportantRepo",
    "isDisabled": false,
    "isInMaintenance": false
}
```

After that, the repository should be visible again in Azure DevOps. You might need to refresh the page again.

I hope this article will help you and save you some stress!
