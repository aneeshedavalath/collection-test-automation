# Collection Test Automation with Bruno and GitHub Actions
This repository contains automated collection tests using the Bruno CLI and continuous integration with GitHub Actions.
## Table of Contents

- [Overview](#overview)
- [Setup](#setup)
- [Workflow for Multiple Collections](#workflow-for-multiple-collections)

## Overview

This project is designed to automate the testing of requests inside collections using Bruno, a CLI tool for API testing. The tests are automatically run for each collection on every push to the main branch using GitHub Actions.

## Setup

To set up the project, follow these steps:

1. **Download and Install Bruno:**
   Download and install Bruno from [here](https://www.usebruno.com/downloads).

2. **Create a New Repository:**
   Create a new repository on GitHub.

3. **Clone the Repository:**
   Clone the repository to your local system:

    ```bash
    git clone https://github.com/your-username/collection-test-automation.git
    ```

4. **Open Bruno and Create a New Collection:**
   Open Bruno and create a new collection.

5. **Add a Name for Your Collection:**
   Add a name for your collection and choose the location. The location should be the cloned repository folder.

6. **Add a New Request to the Collection:**
   Add a new request to that collection.

7. **Add an HTTP Request or a GraphQL Request:**
   You can add an HTTP request, a GraphQL request, or import from a cURL command.

8. **Add an Environment:**
   Add an environment by selecting the top right corner dropdown for environment selection and click on the configure option. You can add an environment and its variables here.

9. **Send Request:**
   Send the request to check if it is working properly.

10. **Add Test Case:**
    Click on the Tests tab and add a test case for that request. Click on the send request button to run the test.

11. **Push Changes to Repository:**
    After verifying the tests, push these changes to the repository.

    ```bash
    git add .
    git commit -m "Add collection tests"
    git push origin main
    ```

12. **Set Up GitHub Actions Workflow:**
    On the GitHub page, click on the Actions tab and click on the [set up a workflow yourself](https://github.com/aneeshedavalats/collection-test-automation/new/main?filename=.github%2Fworkflows%2Fmain.yml&workflow_template=blank).

13. **Add Workflow YAML File:**
    Add the workflow YAML file (`.github/workflows/single-collection-workflow.yml`) and commit the changes.

```yaml
name: Run Single Collection Tests

on:
  push:
    branches:
      - main

jobs:
  run-single-collection-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js environment
        uses: actions/setup-node@v4.0.2
        with:
          node-version: '20'

      - name: Install Bruno CLI
        run: npm install -g @usebruno/cli

      - name: Run single collection tests
        run: bru run --env qa --env-var password=${{ secrets.PASSWORD }} -o results.html -f html --tests-only
        working-directory: collections/pos
        continue-on-error: true

      - name: Upload test results
        uses: actions/upload-artifact@v3
        with:
          name: test-results
          path: collections/pos/results.html
```

14. **Add Secrets to GitHub:**
    If the workflow YAML file contains any secrets, add them to the Actions under the Secrets and variables in the Settings tab. Choose New repository secret, add the variable name in uppercase letters, and add the secret value. Click on the Add secret button.

15. **View Job Results:**
    Once the job run is completed, the result will be uploaded as an artifact. You can download and view the results from the GitHub Actions page.

## Workflow for Multiple Collections
**Workflow YAML File:**
    Add the workflow YAML file (`.github/workflows/multiple-collections-workflow.yml`) and commit the changes.

```yaml
name: Run Multiple Collection Tests

on:
  push:
    branches:
      - main

jobs:
  run-multiple-collection-tests:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        collection:
          - pos
          - pos-duplicate

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js environment
        uses: actions/setup-node@v4.0.2
        with:
          node-version: '20'

      - name: Install Bruno CLI
        run: npm install -g @usebruno/cli

      - name: Run Bruno tests
        run: bru run --env qa --env-var password=${{ secrets.PASSWORD }} -o results-${{ matrix.collection }}.html -f html --tests-only
        working-directory: collections/${{ matrix.collection }}
        continue-on-error: true

      - name: Upload test results
        uses: actions/upload-artifact@v3
        with:
          name: test-results-${{ matrix.collection }}
          path: collections/${{ matrix.collection }}/results-${{ matrix.collection }}.html
```

