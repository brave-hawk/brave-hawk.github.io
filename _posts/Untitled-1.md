# Setting Up CI/CD Pipelines for Salesforce: Part 2

In this follow-up post, we'll dive into configuring our CI/CD pipelines for the **LucidSF_Integration** and **LucidSF_Prod** orgs. Specifically, we'll set up a GitHub Action to automate integration testing for pull requests and prepare secrets for secure authentication.

---

## Preparing the Integration Pull-Request GitHub Action

Our goal is to automate testing so that any **pull request** made from the development branches runs against the **LucidSF_Integration** org. This will help catch issues early before merging changes into the main branch.

### Setting Up the Pull-Request GitHub Action

1. **Create a pull-request YAML file**: We'll define our first GitHub Action using YAML to specify the workflow for handling pull requests.
2. **Define the Workflow**: The YAML file should include steps to authenticate with the Salesforce org, run tests, and check for any failures.

*📸 Add an image here of a sample YAML setup for clarity.*

---

## Creating GitHub Secrets for the Integration Org

To securely connect to your **LucidSF_Integration** org, we’ll use a GitHub secret:

1. Open **VSCode** and connect to your **Integration org**:
   ```bash
   sf org display -o [your INT alias] --verbose
