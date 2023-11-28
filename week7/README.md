# Week 7: Containerization, Extra Topics, Wrap-Up!

## Quiz

There is a [**second take-home quiz**](QUIZ2.md) at the end of this week!

## Presentations

### Tuesday

* GitHub Actions - Variables
* Beta-testing the Quiz

&nbsp; 

* [GitHub Actions + Docker assignment](ASSIGN1.md)

### Thursday

* GitHub Actions - Secrets
* Group activity and Goodbyes!

## Discussion: GitHub Actions - Managing Secrets

GitHub Secrets are a strategy for including sensitive information in a workflow. This removes the need to, for example, store sensitive credentials on certain runners. GitHub manages the secrets, stores them under industry-standard encryption and ensures that only those entities you specify can access the secrets. Secrets are typically used for deployment tasks (such as deploying your updated website to production), but might also be used for tasks such as logging into services to obtain data, license keys for specialized processing pipeline tools and more.

[GitHub's reference page on Secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions) is a great place to start learning about and understanding Secrets. We'll cover the basics here, but definitely refer to the GitHub page as well for more details!
