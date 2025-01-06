+++
title = "How to Use Different Git Accounts for Different Folders"
date = 2025-01-06
description = "As developers, we often juggle multiple Git accounts—one for personal projects, another for work, and perhaps even a third for open-source contributions. Managing these accounts can get tricky, especially when committing to different repositories in different folders."
+++

![Image](banner.webp)

As developers, we often juggle multiple Git accounts—one for personal projects, another for work, and perhaps even a third for open-source contributions. Managing these accounts can get tricky, especially when committing to different repositories in different folders. Thankfully, Git provides a way to manage multiple identities efficiently using the includeIf directive. In this article, we’ll explore how to configure Git to automatically switch accounts based on the folder you’re working in.
<!-- more -->
## Why Use Multiple Git Accounts?

Using multiple Git accounts ensures that your commits are tagged with the correct identity for each project. This distinction is especially important when contributing to:

* Personal repositories under your own name
* Professional projects tied to your employer’s GitHub, GitLab, or Bitbucket account.
* Open-source contributions, where you might want a separate identity for better community interaction.

## Step-by-Step Guide to Configure Git for Multiple Accounts

Here’s how you can set up Git to use different accounts for different folders.
1. Modify Your Global Git Configuration

Open your global Git configuration file, usually located at `~/.gitconfig`. Add a default user configuration along with `includeIf` directives for specific folders. For example:

```bash
[user]
    email = default_email@gmail.com
    name = Default Name

[includeIf "gitdir:~/Work2/"]
    path = ~/Work2/.gitconfig

[includeIf "gitdir:~/Work/"]
    path = ~/Work/.gitconfig

[push]
    autoSetupRemote = true
```
Here’s what this configuration does:

* Sets a global default email and name for Git commits.
* Specifies that repositories under `~/Work2/` will use a custom configuration from `~/Work2/.gitconfig`.
* Similarly, repositories under `~/Work/` will use a configuration file at `~/Work/.gitconfig`.

2. Create Custom Configuration Files

   For each directory, create a separate Git configuration file to specify the user details you want Git to use.

Example: `~/Work2/.gitconfig`
```bash
[user]
    email = email2@gmail.com
    name = Name2
```
Example: `~/Work/.gitconfig`
```bash
[user]
    email = email3@gmail.com
    name = Name3
```
3.  Verify Your Configuration

    Navigate to a repository inside one of these folders and check the effective user configuration:

```bash
cd ~/Work2/some-repo

git config --get user.email
git config --get user.name
```
Repeat the same for other directories to ensure Git is applying the correct configuration.

## Where to Find the Global Git Configuration File

The location of the global Git configuration file depends on your operating system:
* macOS and Linux: The global Git configuration file is typically located at `~/.gitconfig`. You can view or edit it using any text editor, for example:
```bash
nano ~/.gitconfig 
```
* Windows: The global Git configuration file is usually located at `C:\Users\<YourUsername>\.gitconfig`. You can open it with a text editor like Notepad or VS Code.

To verify its location, you can use the following command:
```bash
 git config --global --list --show-origin
```

## How to Generate an SSH Key by Email Address
Generating an SSH key for a specific email address is a common requirement when setting up Git. Here’s how you can do it:

1. Open a Terminal:
* On macOS or Linux, open the Terminal.
* On Windows, use Git Bash or your preferred terminal emulator.
2. Run the SSH Key Generation Command:
   Replace your_email@example.com with your email address and specify a unique filename:
```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f work1
```
If you are using a legacy system that doesn't support the Ed25519 algorithm, use:
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f work1
```
or 
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f ~/.ssh/work1
```

This command generates an RSA key with 4096 bits and saves it to the file `~/.ssh/work1`. You can replace `work1` with a different name for other accounts.

## How It Works

The `[includeIf]` directive allows Git to conditionally include configuration files based on the directory path. The `gitdir:` condition checks if the current repository resides in the specified directory or any of its subdirectories.

For example:
* If you’re in `~/Work2/project-a`, Git uses `~/Work2/.gitconfig`.
* If you’re in `~/Work/project-b`, Git uses `~/Work/.gitconfig`.

## Troubleshooting Tips
1. Absolute Paths: Ensure paths in the `gitdir:` condition are absolute and end with a `/`. For example, use `gitdir:~/Work2/` instead of `gitdir:Work2`.
2. Precedence: Git configuration files are prioritized as follows:
* Repository-specific .git/config.
* includeIf-based configurations.
* Global `~/.gitconfig`.
* System-wide `/etc/gitconfig`.
3. Debugging: If Git isn’t picking up the expected configuration, use the `git config --list --show-origin` command to see which configuration files are being applied.

## Benefits of This Approach
* Automation: No need to manually switch accounts for each repository.
* Consistency: Prevent accidental commits using the wrong email or name.
* Scalability: Easily add more configurations for additional folders as needed.

## Conclusion
Managing multiple Git accounts doesn’t have to be a hassle. With the power of includeIf directives, you can ensure Git automatically uses the correct identity for each folder. This setup not only saves time but also avoids the confusion of mixing personal and professional commits.

Configure your Git accounts today and enjoy seamless identity management across all your projects!





