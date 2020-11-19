# Ops Planner Notify Action

Github action for notifying the [Ops Planner](https://github.com/bluedrop-learning-networks/bln-ops-planner-service) service.

## Usage

| Parameter    | Requirement | Description                                                         |
| ------------ | ----------- | ------------------------------------------------------------------- |
| `url`        | `required`  | Ops Planner service URL.                                            |
| `key`        | `required`  | Ops Planner service auth token.                                     |
| `author`     | `required`  | Author email. Required, since email is not github-actions contexts. |
| `project`    | `optional`  | Project name. Calculated.                                           |
| `imageTag`   | `optional`  | Target project's docker image tag. Calculated. E.g "github.sha".    |
| `branch`     | `optional`  | Commit branch. Calculated.                                          |

### Example

```yaml
name: Build and Publish app
on: push
jobs:
  publish:
    name: Publish Image to GitHub Package Registry
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1
    - name: Login to Github Packages registry
      uses: docker/login-action@v1
      with:
        registry: docker.pkg.github.com
        username: ${{ github.actor }}
        password: ${{ github.token }}
    - name: Push to GitHub Packages
      uses: docker/build-push-action@v2
      with:
        push: true
        tags: ${{ github.repository }}:${{ github.sha }}

  notify:
    name: Notify Ops Planner of Update
    runs-on: ubuntu-latest
    needs: publish
    steps:

    - name: Notify Ops Planner
      uses: bluedrop-learning-networks/bln-ops-planner-notify-action@main
      with:
        url: https://plan.service.com/notify
        key: secret.OPS_PLANNER_TOKEN
        author: john@example.com
```
