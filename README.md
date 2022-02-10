# .github

This is a special repository in github. It must be public.

It serves two purposes:

- Providing workflow templates in the `workflow-templates` folder
- Providing reusable workflows in the `.github/workflows` folder

Both of those objectives aim to provide standardization for github action workflows.

## Workflow Templates

Github requires that workflow templates be stored in a repository named `.github`.
More info is available [here](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization)

## Reusable Workflows

Github requires that all reusable workflows either be stored in the repository where they are used or a public repository.
Since this needs to be a public repository in order to do workflow templates, this is a natural place to store reusable workflows as well.
More information about reusable workflows is available [here](https://docs.github.com/en/actions/using-workflows/reusing-workflows)