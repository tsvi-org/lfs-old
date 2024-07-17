# Repository Migration Workflow

This repository hosts an automated workflow designed to facilitate the migration of code changes, including all Git Large File Storage (LFS) objects, from an old legacy repository to a new repository. The primary goal is to ensure a seamless transition of all project assets, maintaining the integrity and history of both regular files and large files managed by Git LFS.

## Workflow Overview

The migration process is powered by a GitHub Actions workflow defined in `.github/workflows/lfs.yml`. This workflow is triggered either manually (`workflow_dispatch`) or on a scheduled basis (`cron`), making it flexible for ongoing synchronization needs or periodic updates.

The workflow consists of 2 main job:

1. **LFS Sync Job**: This job is responsible for transferring all Git LFS objects from the legacy repository to the new repository. It ensures that all large files are correctly migrated, preserving their version history.

2. **Regular Sync Job**: After the LFS synchronization is complete, this job pushes all regular file changes to the new repository, excluding LFS objects to avoid duplication.

### Key Features

- **LFS Object Migration**: Ensures that all Git LFS objects are correctly transferred to the new repository, preserving the large file versioning.
- **Full History Preservation**: By fetching the entire commit history (`fetch-depth: 0`), the workflow maintains the comprehensive history of both regular and LFS-tracked files.
- **Automated Sync**: Configured to run on a schedule, this workflow automates the process of keeping the new repository up-to-date with the legacy repository's changes.
- **Secure**: Utilizes SSH keys for authentication, ensuring secure communication between the source and target repositories.

### Workflow Steps

1. **Checkout**: Clones the legacy repository, fetching all branches and tags, along with their respective Git LFS objects.
2. **LFS Sync**: Transfers all Git LFS objects to the new repository, ensuring that large files are correctly migrated.
3. **Regular Sync**: After successful LFS synchronization, pushes all regular file changes to the new repository, excluding LFS objects to avoid duplication.

## Usage

To manually trigger the workflow:

1. Navigate to the "Actions" tab of this repository.
2. Select the "LFS and Regular Sync Workflow" from the list of workflows.
3. Click on "Run workflow", choose the branch you wish to synchronize, and then click "Run workflow" again.

For scheduled runs, the workflow is configured to run automatically based on the cron schedule set in the `lfs.yml` file.

## Prerequisites

Before you begin, ensure you have the following:
- Git and Git LFS installed on your local machine.
- Read and write access to both the source (legacy) and target (new) repositories.
- GitHub Actions enabled on your GitHub account.
- SSH keys set up for authentication with the target repository.

## Troubleshooting

Encountering issues? Check out our troubleshooting guide:
- **LFS Sync Errors**: Ensure Git LFS is correctly set up and that you have sufficient storage and bandwidth available on GitHub.
- **Authentication Failures**: Verify your SSH keys are correctly set up and that the service account has the necessary permissions.

## FAQs

**Q: Can I migrate specific branches only?**  
A: Yes, the workflow can be configured to migrate specific branches. Modify the `lfs.yml` workflow file to specify the branches you wish to include.

**Q: What happens to open pull requests in the old repository?**  
A: This workflow does not migrate open pull requests. You will need to manually recreate them in the new repository.

## Contributing

We welcome contributions! Please see our contributing guidelines for how to propose changes, report issues, or submit pull requests.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Git [LFS](https://git-lfs.github.com/) - Git extension for versioning large files.
- GitHub Actions for making automated workflows possible.


## Configuration

Before using the workflow, ensure the following environment variables are correctly set in your GitHub repository secrets:

- `SSH_PRIVATE_KEY`: The SSH private key used for authentication with the target repository.
- `TARGET_ORG`: The organization of the target repository.
- `TARGET_REPO`: The name of the target repository.
- `SERVICE_ACCOUNT_EMAIL`: The email associated with the service account performing the migration.
- `SERVICE_ACCOUNT_NAME`: The name of the service account.
- `TARGET_REPO_SSH_KEY`: The SSH private key used for authentication with the target repository. This key should be added as a deploy key to the target repositories. See the section "Adding a Deploy Key for Repository Deployment" for instructions on how to add the deploy key.

## Adding a Deploy Key for Repository Deployment

To enable the `webfactory/ssh-agent@v0.9.0` action to deploy changes to the target repository, a deploy key must be added to the target repository. Deploy keys are SSH keys that grant access to a single GitHub repository. This section outlines the steps to add a deploy key and configure the workflow to use this key.

### Step 1: Add the SSH Key to the Target Repository

1. Navigate to the GitHub page of your **target** repository.
2. Go to **Settings** > **Deploy keys**.
3. Click on **Add deploy key**, enter a title for the key, and paste the content of your SSH **public** key into the key field.
4. Ensure that the **Allow write access** option is checked if you want this key to have write access to the repository, allowing you to push changes.
5. Click **Add key**.

### Step 2: Add the Private Key to GitHub Secrets

1. Navigate to the GitHub page of your source repository where the workflow is configured.
2. Go to **Settings** > **Secrets and variables** > **Actions**.
3. Click on **New repository secret**.
4. Name the secret `TARGET_REPO_SSH_KEY` and paste the content of your SSH private key into the value field.
5. Click **Add secret**.

### Step 3: Update the Workflow to Use the Deploy Key

Ensure your workflow file uses the secret for the SSH key. The `webfactory/ssh-agent@v0.9.0` action should be configured to use the `TARGET_REPO_SSH_KEY` secret, allowing the workflow to authenticate with the target repository using the deploy key, enabling it to push changes securely.

## Considerations and potential constraints

- The workflow is designed to work with repositories that have a manageable number of LFS objects. For repositories with a large number of LFS objects, consider breaking down the migration into smaller batches to avoid timeouts or resource constraints.

- The workflow is configured to fetch the entire commit history of the legacy repository (`fetch-depth: 0`). This ensures that all commits and LFS objects are fetched, but it may impact the workflow's performance for repositories with extensive history.

- The workflow uses SSH keys for authentication with the target repository. Ensure that the SSH keys are correctly set up and have the necessary permissions to push changes to the target repository.

- The jobs are run on GitHub-hosted runners, which may have resource limitations. For large repositories or repositories with a significant number of LFS objects, consider running the workflow on larger runners or self-hosted runners to avoid resource constraints.

- The workflow is designed to synchronize changes from the legacy repository to the new repository. It does not handle bidirectional synchronization or conflict resolution. Ensure that the new repository is set as the primary repository for future changes to avoid conflicts.

- The workflow includes simple commands to clone, fetch, and push changes between repositories. For more complex migration scenarios or repositories with specific requirements, consider customizing the workflow to meet your needs. The idea here is that this is a starting point for a more complex workflow. There is definitely room for improvement and customization. That includes integrating actions from the marketplace, adding more error handling, more suffisticated orchestration, etc.

- Each job is configured with ubuntu-latest as the runner. If you need to use a different operating system or runner type, you can modify the runs-on parameter in the workflow file. For example, you can use windows-latest or macos-latest for Windows or macOS runners, respectively. Another reason why you might want to use a different runner is that you may need more resources than the default runner provides. For example, if you have a large repository with a lot of LFS objects, you may need more memory or disk space than the default runner provides. In that case, you can use larger runners or self-hosted runners to avoid resource constraints.

## Disclaimer

This workflow is intended for use in scenarios where a full migration of a repository's history, including LFS objects, is required. It is recommended to test the workflow on a sample repository before applying it to a production repository to ensure compatibility and desired outcomes.
