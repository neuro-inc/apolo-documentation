# Checkout

This batch mode action checkouts a repository to the provided volume to make it available in your workflow. By default, it only fetches a single commit.

### Quick example:

```
tasks:
  - id: checkout
    action: gh:apolo-actions/checkout@v1
    args:
      clone_uri: https://github.com/apolo-actions/checkout.git
      checkout_to: "storage:"
      checkout_subpath: dir/to/clone/into
```

### Arguments:

#### `clone_uri`

The URL of the Git repository you want to clone. Both `https://` and `git@` notations are supported.

**Example**

```
args:
  clone_uri: https://github.com/apolo-actions/checkout.git
```

Note that you should provide a `ssh_key_secret` when using the SSH clone notation (`git@...`).

#### `clone_depth`

Number of commits to fetch. `"1"` by default, which means only a single commit will be cloned. To clone the entire repo, set this to `"0"`.

**Example**

```
args:
  clone_depth: "10"
```

#### `ref`

Name of a branch, Git tag, or a commit SHA to fetch. Uses the remote HEAD by default.

**Example**

```
args:
  ref: main
```

#### `ssh_key_secret`

A URI of a [secret](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/secret "mention") that contains an SSH private key to use for cloning.

If you're using a Unix machine with default configuration, you can upload your current private key by using this command:

```
apolo secret add git_ssh_private_key @~/.ssh/id_rsa
```

**Example**

```
args:
  ssh_key_secret: secret:git_ssh_private_key
```

#### `checkout_to`

A URI of a storage folder or disk to use as a volume for checkout. When using a disk, you can specify the directory to checkout via `checkout_subpath`.

Note that the corresponding directory should exist for `storage:` volumes. If you want to automatically create a sub-directory, use `checkout_subpath`.

**Examples**

Storage-based volume

```
args:
  checkout_to: storage:dir/to/checkout
```

Disk-based volume

```
args:
  checkout_to: disk:disk_name_or_id
```

#### `checkout_subpath`

A relative path under `checkout_to` to use as a cloning destination directory. If such folder doesn't exist, it will be automatically created.

**Example**

```
args:
  checkout_subpath: store/repo_content/here
```

#### `erase_subpath`

Enables purging of the directory specified by `checkout_subpath`. Use with caution, as this is the same as running the `rm -rf checkout_subpath` command. To enable this feature, set input to `"true"`.

**Example**

```
args:
  erase_subpath: "true"
```

### Outputs:

#### `head_sha`

A SHA of the commit under the current HEAD in the cloned repository. This output's main goal is to make the action cache-friendly - even if the repository didn't change, that output will be the same and the depending action can safely use the cache.

{% hint style="info" %}
Feel free to check the [Checkout action repository](https://github.com/apolo-actions/checkout).
{% endhint %}
