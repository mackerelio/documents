---
Title: Managing monitoring rules in GitHub
Date: 2015-08-14T18:30:17+09:00
URL: https://mackerel.io/docs/entry/advanced/monitors-github
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450105580325
---

By using mkr, actions such as saving monitoring rules to local files (`pull`), diffing a local file to Mackerel’s settings (`diff`), and having the contents of a local file reflect Mackerel’s settings (`push`).

To see more information about each command, please check the `cli` [help page](./cli)

In this document we will introduce how to manage monitoring rules in [GitHub](https://github.com/) using mkr.

## Monitoring rule management with mkr

As for ways to manage monitoring rules with mkr, there are 2 main patterns, making changes in both the web UI and code, or only in code, which will be described next.

- Make changes in both the web UI and code
  - Use both `pull` and `push`
- Make changes to monitoring rules only in code, without using the web UI
  - Use only `push`, not `pull`

With mkr monitors, `id` or `name` will be used as a designation for the monitor rule.
`id` will be assigned by Mackerel when a new monitoring rule is created.
`name` is given by the user.


If you will be doing management based on the former, `id`, so it will be necessary to also include the `id` in the JSON file which will be managed.
If you will be doing management based on the latter, `name`, it won’t be necessary to include the `id` in the JSON file. However it will be necessary that the `name` is not duplicated.

If the `name` is duplicated in the JSON file and in Mackerel, mkr will designate monitoring rules based on the `id`.
Regardless of whether or not the `name` has been duplicated, if and `id` doesn’t exist for each monitoring rule is the JSON, mkr will consider it invalid JSON.


## Using `pull` and `push`

Here we will manage monitoring rules based on their `id`. For that reason it will be necessary to obtain the `id`s assigned by Mackerel when the monitoring rules were created.

### Initialization
- Create a repository in GitHub
- Do git clone
  ```
  git clone <repo-url>
  cd <repo-path>
  ```
- Obtain monitoring rules from Mackerel
  ```
  mkr monitors pull
  ```
- Commit to the repository on GitHub
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```

### Confirm synchronization of the repository and Mackerel’s settings
- Bring the latest data from GitHub
  ```
  cd <repo-path>
  git pull
  ```
- Diff against Mackerel
  ```
  mkr monitors diff
  ```
  If there are no differences, you will get the following result
  ```
  Summary: 0 modify, 0 append, 0 remove
  ```

### Making changes to rules (web)
- Making changes to monitoring rules on the web
- Obtain the monitoring rules that were changed from Mackerel
  ```
  cd <repo-path>
  mkr monitors pull
  ```
- Commit and push to repository on GitHub
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```

### Make changes to a saved monitoring rule (code)
- Make changes and do reviews on GitHub, sync changes to master branch
- Obtain the monitoring rules that were changed from Mackerel

  ```
  cd <repo-path>
  mkr monitors pull
  ```
- Diff against Mackerel
  ```
  mkr monitors diff
  ```
- Sync the monitoring rules that were changed to Mackerel
  ```
  mkr monitors push
  ```

### Add new monitoring rules (code) 
- After creating JSON, make changes and do reviews on GitHub, sync changes to master branch
  - Here we will make JSON with no `id`
  - For more information about JSON format please refer to [Registering monitor configuration in API specs](https://mackerel.io/api-docs/entry/monitors#create).
- Diff against Mackerel
  ```
  mkr monitors diff
  ```
- Sync the added monitoring rules with Mackerel
  ```
  mkr monitors push
  ```
- Obtain the `id`s Mackerel assigned to the added monitoring rules
  ```
  mkr monitors pull
  ```
- Commit and push to repository on GitHub
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```

## Using just `push`

Here we will manage monitoring rules based on their `name`.
It’s necessary to make sure that each monitoring rule’s `name` hasn’t been duplicated.
Also, making changes to monitoring rules in the web UI is not advised.
If you have accidentally made changes in the web UI, it will be necessary to either construct the JSON manually or execute a pull.

### Initialization
- Create a repository in GitHub
- Do git clone
  ```
  git clone <repo-url>
  cd <repo-path>
  ```
- Obtain monitoring rules from Mackerel
  ```
  mkr monitors pull
  ```
- Commit and push to repository on GitHub
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```

### Confirm synchronization of the repository and Mackerel’s settings
- Bring the latest data from GitHub
  ```
  cd <repo-path>
  git pull
  ```
- Diff against Mackerel
  ```
  mkr monitors diff
  ```
  If there are no differences, you will get the following result

  ```
  Summary: 0 modify, 0 append, 0 remove
  ```

### Making changes to saved monitoring rules (code)
- Make changes and do reviews on GitHub, sync changes to master branch
- Obtain the monitoring rules that were changed from Mackerel
  ```
  cd <repo-path>
  mkr monitors pull
  ```
- Diff against Mackerel
  ```
  mkr monitors diff
  ```
- Sync the monitoring rules that were changed with Mackerel
  ```
  mkr monitors push
  ```

### Add new monitoring rules (code) 
- After creating JSON, make changes and do reviews on GitHub, sync changes to master branch
  - Here we will make JSON with no `id`
  - For more information about JSON format please refer to [Registering monitor configuration in API specs](https://mackerel.io/api-docs/entry/monitors#create).
- Diff against Mackerel
  ```
  mkr monitors diff
  ```
- Sync the monitoring rules that were added to Mackerel
  ```
  mkr monitors push
  ```
  - For monitoring rules to be judged identically based on `name`, it’s not necessary to do a pull to sync the `id`s.

### Syncing with rules on Mackerel

If you have accidentally made changes in the web UI, you can renew those changes (?) with the following process.

- Obtain the monitoring rules that were changed from Mackerel
  ```
  cd <repo-path>
  mkr monitors pull
  ```
  - By doing a pull you will get JSON with the `id`s attached
- Delete the `id` file from the JSON
  - Even if the `id` field still exists, it won’t cause any problems so it’s ok to leave it as it is.
- Diff against Mackerel
  ```
  mkr monitors diff
  ```
- Commit and push to repository on GitHub
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```
