# Bitbucket Pipelines starter kit for NPM

This is a starting point for using Bitbucket Pipelines in a JavaScript project
that uses NPM. It does the following:

* Automatic tasks:
  * All branches: Build the project
  * `master` branch: Deploy the build artifact (`npm publish`)
* Manual tasks (can be invoked within the Bitbucket UI):
  * Release major version
  * Release minor version
  * Release patch

The manual release tasks

  * Incrememnt the major, minor, or patch component of the `version` property
    in the project's pom.xml
  * Commit the change to pom.xml to `develop`
  * Tag `develop` with the updated version
  * Merge `develop` into `master`

For this to work, you should follow these rules:

  * Create a new branch for all new features, bugfixes, etc.
  * Merge branches into `develop` only. Don't merge directly into `master`.
  * Don't merge branches with broken builds into `develop`.
  * Only use [semver](https://semver.org/) versions in package.json, but stick
    to `MAJOR.MINOR.PATCH` (no suffixes, e.g. `-beta`) otherwise version bumping
    may produce unexpected results.

**Note:** The build steps in bitbucket-pipelines.yml assume that package.json
contains certain npm scripts. Adjust bitbucket-pipelines.yml according to your
own project's npm scripts.

npmjs.com provides private npm hosting at a very reasonable price.
Alternatively, you can set up your own private npm registry using Verdaccio.
Instructions to setup Verdaccio (and other Artifact servers) are provided in
[steve-taylor/artifact-server-config](https://github.com/steve-taylor/artifact-server-config).

## Configuring Bitbucket Pipelines

bitbucket-pipelines.yml by itself isn't quite enough to fully configure your
project for Bitbucket Pipelines. You will need to provide some additional
settings in Bitbucket.

### Environment variables

The following environment variables need to be set withing Bitbucket.
Fortunately, you can set all of these at the team level and they will be
applied to all repositories within the team.

| Name                             | Example                                       | Description                                           |
|----------------------------------|-----------------------------------------------|-------------------------------------------------------|
| `DEPLOYER_NAME`                  | `Deploy Bot`                                  | Deployment script name (appears in git logs)          |
| `DEPLOYER_EMAIL`                 | `deploybot@example.com`                       | Deployment script email address (appears in git logs) |
| `NPM_REGISTRY_URL`               | `https://registry.npmjs.org/`                 | NPM registry URL                                      |
| `NPM_TOKEN`                      |                                               | NPM auth token                                        |

### ssh

Unfortunately, the ssh keys provided by Bitbucket Pipelines don't allow tasks
to push back to their git repository. You will need to generate a new ssh key
pair that allows Bitbucket Pipelines to push to git.

1. Go to *Settings* / *Security* / *SSH keys* in your Bitbucket team.
2. Click *Add key*
3. Generate an ssh key pair and paste the **public** key into into the *Key*
   field. (The dialog contains links to instructions to generate an ssh key
   pair.)
4. Provide a label and click *Add key* to finish adding the team-level ssh
   key.
5. Navigate to your repo and go to *Settings* / *Pipelines* / *SSH keys*.
6. If there is already a key, delete it.
7. Provide the private and public keys from step 2.

For additional projects, repeat steps 5 to 7.

**Note:** Bitbucket will log warnings each time it pushes using the team-level
ssh key, as it is a deprecated feature and they unfortunately recommend using
an individual account's ssh key instead. You're quite welcome to follow that
recommendation if it makes you sleep better at night.
