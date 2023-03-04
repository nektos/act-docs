# GitHub Enterprise

Act supports using and authenticating against private GitHub Enterprise servers.
To use your custom GHE server, set the CLI flag `--github-instance` to your hostname (e.g. `github.company.com`).

Please note that if your GHE server requires authentication, we will use the secret provided via `GITHUB_TOKEN`.

Please also see the [official documentation for GitHub actions on GHE][actions-enterprise] for more information on how to use actions.

[actions-enterprise]: https://docs.github.com/en/enterprise-server@3.0/admin/github-actions/about-using-actions-in-your-enterprise
