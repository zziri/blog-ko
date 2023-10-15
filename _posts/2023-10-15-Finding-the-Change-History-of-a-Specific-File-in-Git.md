---
title: "Finding the Change History of a Specific File in Git"
categories:
  - IT
---

When we need to find the history of adding, deleting, or modifying a specific file, let's look at how to search for PRs or commits.

## Command
If you want to find commits that added, deleted, or changed the some-script.js file...

```shell
git log --oneline --all -- "**/some-script.js"
```

## Interpretation
```shell
git log --oneline --all -- "**/filename.ext"
```

Let's understand the meaning of each option in the above command:

1. **--oneline**:
  - This option displays each commit in one line.
  - Typically, only the commit hash and title are displayed. This provides a concise view of the long log output, helping to quickly grasp the overall flow of the log.
2. **--all**:
  - Displays logs of all branches and tags.
  - By default, git log shows only the log of the currently checked-out branch. But with the --all option, you can see the commit logs of all branches and tags in the repository.
3. **-- "\*\*/filename.ext"**:
  - Using patterns, it queries only the change history of a specific file or directory.
  - The **/ part signifies that regardless of which directory path it's in, it will search for the file with that filename. Hence, it can query all change histories of files named filename.ext in the repository.


In conclusion, this command displays a concise one-line representation of the entire change history of files named filename.ext from all branches and tags within the repository.