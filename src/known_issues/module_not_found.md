# `MODULE_NOT_FOUND`

A `MODULE_NOT_FOUND` during `docker cp` command [#228](https://github.com/nektos/act/issues/228) can happen if you are relying on local changes that have not been pushed. This can get triggered if the action is using a path, like:

```yaml
- name: test action locally
  uses: ./
```

In this case, you _must_ use `actions/checkout@v2` with a path that _has the same name as your repository_. If your repository is called _my-action_, then your checkout step would look like:

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v2
    with:
      path: "my-action"
```

If the `path:` value doesn't match the name of the repository, a `MODULE_NOT_FOUND` will be thrown.
