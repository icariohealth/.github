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
    uses: 'icariohealth/.github/.github/workflows/cfn-linting.yml@main'
    with:
      cfn_files: '*cfn.yml'
    secrets:
      ci_token: '${{ secrets.NOVU_CI_TOKEN }}'
```

### Common Linting

Runs yaml and markdown linting for your workflow.

Inputs & Secrets:

- `ci_token` - should be a github token that can be used to clone other repositories
- `action_ref` - which branch to use when pulling in github actions, defaults to main

Example Use:

```yaml
  ApplyCommonLinting:
    uses: 'icariohealth/.github/.github/workflows/common-linting.yml@main'
    secrets:
      ci_token: '${{ secrets.NOVU_CI_TOKEN }}'
```

### Docker Linting

Runs hadolint and twistlock. Requires building and publishin the docker image to ECR first.
Hadolint operates on the Dockerfile, but twistlock operates on a complete docker image.

Inputs & Secrets:

- dockerfile_folder: folder within the repository where the `Dockerfile` can be found
- hado_ignores: list of space-separated hado rules to ignore
- aws_account_id: the aws account id where we'll pull the ECR image from
- action_ref: the branch of the github actions repository to clone (defaults to main)
- ecr_registry: the name of the ecr registry
- docker_image_tag: the tag indicating a specific version/image to scan
- ci_token: github token with permissions to clone the github-actions repository
- twistlock_key_id: twistlock key identifier
- twistlock_key: twistlock key/password
- aws_access_key_id: AWS access key id used to authenticate with ECR
- aws_access_key: AWS access key used to authenticate with ECR
- aws_region: AWS region used to authenticate with ECR

Example Use:

```yaml
  ApplyDockerLinting:
    needs: BuildAndPushDockerImage
    uses: 'icariohealth/.github/.github/workflows/docker-linting.yml@main'
    with:
      dockerfile_folder: '.'
      ecr_account_id: '822373129316'  # WE
      image_name: 'novu/ci-cache/qaautomationci'
      image_tag: '${{ env.DOCKER_IMAGE_TAG_FOR_CI_RUN }}'
    secrets:
      ci_token: '${{ secrets.NOVU_CI_TOKEN }}'
      twistlock_key_id: '${{ secrets.TWISTLOCK_KEY_ID }}'
      twistlock_key: '${{ secrets.TWISTLOCK_KEY }}'
      aws_access_key_id: '${{ secrets.AWS_ACCESS_KEY_ID }}'
      aws_access_key: '${{ secrets.AWS_SECRET_ACCESS_KEY }}'
      aws_region: '${{ secrets.AWS_REGION }}'
```

### Kustomize Linting

Runs kustomize on provided build paths.

Inputs & Secrets:

- `ci_token` - should be a github token that can be used to clone other repositories

- `build_path` - uses a matrix strategy

```yaml
    strategy:
      matrix:
        build_path:
          - "overlays/abc"
          - "overlays/def"

```

Example Uses:

#### Standard Environments

```yaml
  ApplyKustomizeLinting:
    needs:
      - 'ApplyCommonLinting'
    uses: 'icariohealth/.github/.github/workflows/kustomize.yml@CLOUD-3258'
    secrets:
      ci_token: "${{ secrets.NOVU_CI_TOKEN }}"
    with:
      build_path: "${{ matrix.build_path }}"
    strategy:
      matrix:
        build_path:
          - "overlays/dev"
          - "overlays/tst
          - "overlays/stg"
          - "overlays/prd"
```

#### Custom Paths

```yaml
  ApplyKustomizeLinting:
    needs:
      - 'ApplyCommonLinting'
    uses: 'icariohealth/.github/.github/workflows/kustomize.yml@CLOUD-3258'
    secrets:
      ci_token: "${{ secrets.NOVU_CI_TOKEN }}"
    with:
      build_path: "${{ matrix.build_path }}"
    strategy:
      matrix:
        build_path:
          - "app-managers/overlays/rc2-axonserver/dev"
          - "app-managers/overlays/rc2-axonserver-secrets/dev"
          - "app-managers/overlays/rc2-program-service/dev"
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
    uses: 'icariohealth/.github/.github/workflows/ruby-linting.yml@main'
    with:
      ruby_version: '2.7.4'
    secrets:
      reek_github_token: '${{secrets.GITHUB_TOKEN}}'
      artifactory_credentials: '${{ secrets.ARTIFACTORY_CREDENTIALS }}'
```

### Tf Linting

Runs several terraform linting tools for your workflow.

Inputs & Secrets:

- `ci_token` - should be a github token that can be used to clone other repositories

Example Use:

```yaml
  ApplyTfLinting:
    uses: 'icariohealth/.github/.github/workflows/tf-linting.yml@main'
    secrets:
      ci_token: '${{ secrets.NOVU_CI_TOKEN }}'
```
