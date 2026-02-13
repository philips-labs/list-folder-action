# List Folder Action

This action lists the folders in the given directory and returns them as a json formatted list.

This is useful for using in subsequent jobs as the matrix command so you can run a job per sub folder.

This is currently a simple composite action. It ensures that all tools needed are installed by running `apt install` commands. Therefore it is advised to run this inside a debian/ubuntu based runner or container.

## Inputs

| Name | Description                                     | Required |
| ---- | ----------------------------------------------- | -------- |
| path | The path of a folder to list the sub folders of | true     |

## Outputs

| Name    | Description                                   |
| ------- | --------------------------------------------- |
| folders | The json formatted string list of sub folders |

## Example

This action can be used to list the folders and input them into a matrix for a subsequent job.

```yaml
jobs:
  find-jobs:
    name: Find Jobs
    container: debian:buster-slim
    outputs:
      folders: ${{ steps.jobs.outputs.folders }}
    steps:
      - uses: actions/checkout@v1

      - id: jobs
        uses: philips-labs/list-folder-action@v1
        with:
          path: ./path/to/folder

  matrix:
    name: Matrix Jobs
    needs: [find-jobs]
    container: ubuntu
    defaults:
      run:
        working-directory: ${{ matrix.folder }}
    strategy:
      matrix:
        folder: ${{ fromJson(needs.find-jobs.outputs.folders )}}
    steps:
      - name: do something
        run: echo ${{ matrix.folder }}
```
