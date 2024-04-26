# Github Actions 

## CI (Continuous integration) 
Merge Code In
## CD (Continuous development) 
Release Code Out

## Uses:
### Code testing before merge/pull-request (CI)
```
name: Node.js CI
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm test
```

### Deployment (CD)
```
name: SSH deploy
on:
  push:
    branches: [ master ]

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    steps:
      - name: executing remote commands
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: /* hosting platform */
          username: /* username to login */
          key: ${{ secrets.SOMESECRETKEY }}
          port: 22
          /* any code that would be needed to manually deploy */
          script: |
            cd ~/projects/my-project
            git pull
            composer update
            rm -rf ./temp/cache
            echo "done"
```
            

### Publish NPM Package
```
name: My npm package
on:
  release:
    types: [created]

jobs:
  build:
    # Your build job configuration goes here
    ...

  publish-npm:
    needs: build
    # Your publish-npm job configuration goes here
    ...
```
            

### Integrate Apps
```
name: Slack Issues
on:
  issues:
    types: [opened]

jobs:
  post_slack_message_on_github_issue:
    runs-on: ubuntu-latest
    steps:
      - uses: rtCamp/action-slack-notify@v2.0.0
        env:
          SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
          SLACK_USERNAME: /* username to login */
          SLACK_CHANNEL: gh-issues
```


### Schedule Background Jobs
e.g. database backup

```
name: Export Firestore Data
on: 
  schedule:
    - cron: '0 0 * * *'  # runs everyday at midnight

jobs: 
  backup:
    runs-on: ubuntu-latest
    steps: 
      - uses: GoogleCloudPlatform/github-actions/setup-gcloud@master
        with:
          service_account_key: ${{ secrets.GCP_SA_KEY }}
          export_default_credentials: true
      - run: gcloud config set project $PROJECT_ID
      - run: gcloud firestore export $BUCKET
```
