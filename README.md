# Nuget Repo Template

This repository is provided as a template repository for the creation of Nuget packages. It contains build and deploy scripts that are executed by the Github Actions feature when code is published to a remote repository that is based of this template.

## Getting started

Following instructions are targetted at bash shell (as per git-bash)

1. Create a new repository via the Aqovia Github site using the  [here](https://github.com/organizations/Aqovia/repositories/new)
    - Select the `nuget-repo-template` as the starter template for the repo
    - Give the repo a unique name eg new-nuget-repo
2. Clone the new repo to your local machine using the standard git mechanism
    - `git clone https://github.com/Aqovia/new-nuget-repo.git`
3. Move into the new repository
    - `cd new-nuget-repo`
4. Set up the project - substitute {YOUR_NEW_PROJECT_NAME} with the correct project name
```bash
NEW_PROJECT={YOUR_NEW_PROJECT_NAME}
mkdir src src/$NEW_PROJECT
dotnet new sln -n $NEW_PROJECT
pushd src/$NEW_PROJECT && dotnet new classlib && popd
dotnet sln add src/$NEW_PROJECT/$NEW_PROJECT.csproj
```
5. If tests are required (example setup) - from the repo root
```bash
NEW_TEST_PROJECT={YOUR_NEW_TEST_PROJECT_NAME}
mkdir test test/$NEW_TEST_PROJECT
pushd test/$NEW_TEST_PROJECT && dotnet new mstest && popd
dotnet sln add test/$NEW_TEST_PROJECT/$NEW_TEST_PROJECT.csproj
``` 
6. Setup the new project to be packages for Nuget repositories
    - set the following in the classlib csproj file
```xml
    <TargetFramework>{YOUR_TARGET_FW_GOES_HERE}</TargetFramework>
        <Description>{YOUR_DESCRIPTION_GOES_HERE}</Description>
		<IsPackable>true</IsPackable>
		<RepositoryType>git</RepositoryType>
    	<RepositoryUrl>{LINK_TO_YOUR_REPOSIORY_GOES_HERE}</RepositoryUrl>
  </PropertyGroup>
```

7. Configure the CI Pipeline
    - open the file `.github/workflows/pipelines.yml`
    - set these variables in the `env` section of the file - set only the variables shown and leave others as they are
```yaml
env:
  # Project name to pack and publish
  PROJECT_NAME: {YOUR_NEW_PROJECT_NAME}
  # GitHub Packages Feed settings
  GITHUB_FEED: {YOUR_GITHUB_FEED_URL}
```
8. Replace the `README.md` (this file) with a relevant README for your project

9. Commit your changes
```bash
git add -A
git commit -m "Initial project setup and config"
git push
```
10. Observe the building of the project on the remote server by going to the Gihub Actions tab in the remote repo ui
11. Create features by branching from the `master` branch
12. Once completed push the changes to the remote and observe the remote build of the branch
    - the CI will create a preview build and push this to the GitHub Feed for testing
13. Once finalised create a pull request to `master` branch via the Github UI
    - the repo is configured to use Squah-Commit on merge to master only (after review process)
    - at this point there is an opportunity to overwrite the squash commit message
    - there is functionlity to parse and extract this message for the release process
    - all merges to master are intended to publish a new semantic version of the project to the Nuget feeds
    - in order to inform the CI pipeline of what your intended semantic version update should be for this change (pr to master) you should include 1 of the following strings
        1. bump: major
            - this will bump the major version during the release process
        2. bump: minor
            - this will bump the minor version during the release process
        3. bump: patch
            - this will bump the patch version during the release process
            - if no matching bump message is found this will be the default upgrade to the version
    - the `RELEASE_NOTE.md` will be automatically updated using the information provided in the squash commit message
        - the subject of the message will become a bullet point under the new version heading
        - the body of the message (and all markdown) will become the content under the bullet point
14. Happy coding! :smiley:





