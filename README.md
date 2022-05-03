# AWS ECR Action

This Action allows you to create Docker images and push into a ECR repository.

## Parameters
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `access_key_id` | `string` | | Your AWS access key id |
| `secret_access_key` | `string` | | Your AWS secret access key |
| `account_id` | `string` | | Your AWS Account ID |
| `app_name` | `string` | | Name of Application |
| `region` | `string` | | Your AWS region |
| `repo` | `string` | | ECR image repo to distribute other than the first array registered in ops-monster |
| `create_repo` | `boolean` | `false` | Set this to true to create the repository if it does not already exist |
| `set_repo_policy` | `boolean` | `false` | Set this to true to set a IAM policy on the repository |
| `repo_policy_file` | `string` | `repo-policy.json` | Set this to repository policy statement json file. only used if the set_repo_policy is set to true |
| `image_scanning_configuration` | `boolean` | `false` | Set this to True if you want AWS to scan your images for vulnerabilities |
| `tags` | `string` | `latest` | Comma-separated string of ECR image tags (ex latest,1.0.0,) |
| `dockerfile` | `string` | `Dockerfile` | Name of Dockerfile to use |
| `extra_build_args` | `string` | `""` | Extra flags to pass to docker build (see docs.docker.com/engine/reference/commandline/build) |
| `cache_from` | `string` | `""` | Images to use as cache for the docker build (see `--cache-from` argument docs.docker.com/engine/reference/commandline/build) |
| `path` | `string` | `.` | Path to Dockerfile, defaults to the working directory |
| `prebuild_script` | `string` | | Relative path from top-level to script to run before Docker build |
| `registry_ids` | `string` | | : A comma-delimited list of AWS account IDs that are associated with the ECR registries. If you do not specify a registry, the default ECR registry is assumed |

## Usage

```yaml
name: Deploy

on:
  push:
    branches:
      - main

env:
  APP_NAME: ops-monster

jobs:
  get-namespace:
    runs-on: [ self-hosted, Linux, X64, prod-k8s-runner ]
    steps:
      - name: Check out code
        uses: actions/checkout@v1
      - name: get image tag and version
        id: vars
        run: |
          echo "::set-output name=sha_short::$(git rev-parse --short HEAD)"
      - uses: bucketplace/aws-ecr-action@main
        with:
          access_key_id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          secret_access_key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          account_id: ${{ secrets.AWS_ACCOUNT_ID }}
          app_name: ${{ env.APP_NAME }}
          region: ap-northeast-2
          tags: ${{ steps.vars.outputs.sha_short }}
          dockerfile: Dockerfile
          create_repo: true
```

## License
The MIT License (MIT)
