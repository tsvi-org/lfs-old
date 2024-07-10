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

## Disclaimer

This workflow is intended for use in scenarios where a full migration of a repository's history, including LFS objects, is required. It is recommended to test the workflow on a sample repository before applying it to a production repository to ensure compatibility and desired outcomes.
