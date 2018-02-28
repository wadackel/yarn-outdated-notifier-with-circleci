# yarn-outdated-notifier with CircleCI

> Example repository using [yarn-outdated-notifier](https://github.com/tsuyoshiwada/yarn-outdated-notifier) in CircleCI



## Setup CircleCI


### 1. Create GitHub Personal access tokens

Please access the page of [Personal access tokens](https://github.com/settings/tokens) and issue a token. You need the scope of `repo`.

Add the issued token to the Environment Variables of CircleCI under the name `GITHUB_API_TOKEN`.


### 2. Configuration

By using `cron` of `workflows` like below you can periodically notify outdated npm package.

```yaml
version: 2

# In order to use cron, define a job for notification.
jobs:
  notify_outdated:
    docker:
      - image: circleci/node:9.3
    working_directory: ~/repo
    steps:
      - checkout
      - run: yarn
      - run: |
          yarn outdated-notifier \
            --labels "yarn-outdated" \
            --assignees "<assignee_name>" \
            --changelogs "changelogs.yml" \
            --api-token $GITHUB_API_TOKEN

# Define a workflow for executing job.
workflows:
  version: 2

  # ... run testing job

  notification:
    jobs:
      - notify_outdated
    triggers:
      - schedule:
          cron: "0 1 * * 4"
          filters:
            branches:
              only:
                - master
```

Please refer to [.circleci/config.yml](./.circleci/config.yml) for the setting file actually used.




## License

[MIT © tsuyoshiwada](./LICENSE)
