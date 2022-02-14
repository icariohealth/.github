# .github

This is a special repository in github. It must be public.

It serves two purposes:

- Providing workflow templates in the `workflow-templates` folder
- Providing reusable workflows in the `.github/workflows` folder

Both of those objectives aim to provide standardization for github action workflows.

## Workflow Templates

Github requires that workflow templates be stored in a repository named `.github`.
More info is available [here](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization)

### Icario Ruby Ci Template

Currently a specific pipeline from one of the ruby projects, not very templatized.

## Reusable Workflows

Github requires that all reusable workflows either be stored in the repository where they are used or a public repository.
Since this needs to be a public repository in order to do workflow templates, this is a natural place to store reusable workflows as well.
More information about reusable workflows is available [here](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

### Common Linting

Runs yaml and markdown linting for your workflow.

Inputs & Secrets:

- `ci_token` - should be a github token that can be used to clone other repositories
- `action_ref` - which branch to use when pulling in github actions, defaults to main

Example Use:

```yaml
  ApplyCommonLinting:
    uses: icariohealth/.github/.github/workflows/common-linting.yml@main
    secrets:
      ci_token: '${{ secrets.NOVU_CI_TOKEN }}'
```

### Cfn Linting

Runs cfn-lint and checkov for your workflow.

Inputs & Secrets:

- `ci_token` - should be a github token that can be used to clone other repositories
- `action_ref` - which branch to use when pulling in github actions, defaults to main
- `cfn_files` - glob relative to the root of the repository, defaults to `*cfn.yml`
- `checkov_skips` - comma-separated list of checkov rules to skip

Example Use:

```yaml
  ApplyCfnLinting:
    uses: icariohealth/.github/.github/workflows/cfn-linting.yml@main
    with:
      cfn_files: '*cfn.yml'
    secrets:
      ci_token: '${{ secrets.NOVU_CI_TOKEN }}'
```

### Tf Linting

Runs several terraform linting tools for your workflow.

Inputs & Secrets:

- `ci_token` - should be a github token that can be used to clone other repositories

Example Use:

```yaml
  ApplyTfLinting:
    uses: icariohealth/.github/.github/workflows/tf-linting.yml@main
    secrets:
      ci_token: '${{ secrets.NOVU_CI_TOKEN }}'
```

### Ruby Linting

Runs reek & rubocop.

Inputs & Secrets:

- `artifactory_credentials` - combined credentials for artifactory
- `reek_github_token` - github token used by reek
- `ruby_version` - ruby version - example: `2.7.4`

Example Use:

```yaml
  ApplyRubyLinting:
    uses: icariohealth/.github/.github/workflows/ruby-linting.yml@main
    with:
      ruby_version: 2.7.4
    secrets:
      reek_github_token: ${{secrets.GITHUB_TOKEN}}
      artifactory_credentials: ${{ secrets.ARTIFACTORY_CREDENTIALS }}
```
