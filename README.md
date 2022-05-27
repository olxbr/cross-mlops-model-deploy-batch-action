# Model Batch Deploy action

This action builds Airflow Dags to deploy batch models.

Steps:
- Valides model and deploy documents
- Builds and save DAGs to Batch Models
- Creates a pull-request to deploy the server 

### Usage

```yaml
on: [push]

jobs:
  build-job:
    runs-on: ubuntu-latest
    name: Deploy
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Choose tag or commit as version
        id: release
        uses: olxbr/tag-or-commit-action@v0.0.10

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_CROSS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_CROSS_SECRET_ACCESS_KEY }}
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
          aws-region: us-east-1
          role-duration-seconds: 3600

      - name: Deploy Model
        id: deploy_model
        uses: olxbr/cross-mlops-model-deploy-batch-action@master
        with:
          # The github repository to pull-request 
          repo_pr: https://github.com/pr_repository_uri
          # OLX github bot user
          git_user: github-bot-user
          # OLX github bot email
          git_email: github-bot-email@email.com
        env:
          GH_TOKEN: ${{ secrets.OLXBR_BOT_PAT_GH_TOKEN }}

      - name: Get the output time
        run: echo "The time was ${{ steps.deploy_model.outputs.time }}"

```