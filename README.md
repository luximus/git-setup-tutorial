# Learning Git and GitHub

Team 3245 and 3166 use Git and GitHub for source code version control. These instructions will guide you through installing and configuring Git on your personal computer and setting up your access to the [GitHub](https://github.com) code repository.

Version control systems maintain a history of all changes made to your software development project. The biggest benefit of using one is that you can easily back out changes to your code if you really mess things up (everyone does at some point). They also allow teams of programmers to make changes to the code at the same time and provide support for merging the changes together. Furthermore, modern version control systems can synchronize changes to your project between multiple copies stored on different servers. Git is probably the most popular version control system, and GitHub wraps Git in an easy-to-use website. GitHub makes it easy to create a remotely hosted copy of a project, free of charge in many cases, and share it with others. As a result, GitHub has become the defacto standard for hosting open source software projects.

All of the instructions below assume you have administrator access to your computer. After you are done, you will be able to access Git repositories on GitHub from your computer without ever needing to enter a password!

NOTE: the Windows instructions are for Windows 10. If you have Windows 11, the instructions likely still work with minor modifications, but I don't yet have access to a Windows 11 machine to verify that.

## Create a GitHub account

The first step is to [create a GitHub account](https://github.com/signup).

## Install Git on macOS

On Mavericks (10.9) or newer, try to run Git from the Terminal:

```
git --version
```

If you don’t have Git already, Terminal will prompt you to install it. The rest of what you need is installed by default or will be installed along with Git. You can skip down to "Create a SSH key."

If you have an older version of macOS, you will need to install Xcode from the App Store, launch Xcode, and install the command line tools when prompted.

## Install Git on Windows

Go to [this link](https://git-scm.com/download/win) and the download will start automatically. See the [Git for Windows website](https://gitforwindows.org) for more information.

## Install Windows Terminal

Go to the Microsoft Store, search for Windows Terminal, and install it. It is free of charge from Microsoft. You will probably want to set Terminal's default shell to PowerShell, which is far superior to the regular command prompt. There is also a built in PowerShell app in Windows that you can use, but Terminal is much nicer. For example, Terminal supports normal cut, copy, and paste keyboard shortcuts, not to mention theming.

## SSH or HTTPS?

When working on a project, you have to *clone* it onto your computer. This is done using one of two services: HTTPS or SSH. HTTPS requires no additional setup, but is slightly less secure and, according to some, has caused issues down the road. SSH is more secure, but can cause issues when working on corporate networks and requires significant setup. There is no consensus on either option, so the following sections show how to set up SSH on our computer. If you would like to use HTTPS, skip to "Configuring Git."

## Install OpenSSH Client on Windows

OpenSSH will give you the `ssh` command, which Git uses behind the scenes to communicate securely with GitHub's servers. macOS has `ssh` installed by default.

In an administrator account, go to Settings, and go to Apps & Features. Click on Optional Features. If OpenSSH Client is not listed under Installed Features, click Add a feature and install it. If you don't see the OpenSSH Client listed, you probably are not in an administrator account.

To make sure this worked, in your regular user account, open Terminal, and from Powershell run `get-command ssh`. The response should look something like `C:\Windows\System32\OpenSSH\ssh.exe`. If not, reboot your computer and try again.

## Configure Git to use the correct `ssh` (Windows)

Open a Terminal, and run the following command to configure Git to use OpenSSH's `ssh` command, substituting `<the git command from above>` with what `get-command ssh` just told you:

```
git config --global core.sshCommand <the git command from above>
```

On macOS, Git works with `ssh` out of the box.

## Create a SSH key (all platforms)

SSH keys are an alternative to entering a username and password to connect to a server using SSH. They make use of public key cryptography to ensure a secure connection.

Run the following in Terminal to create a key, substituting your_email@example.com with your own email address:

```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

If you get an error about the ed25519 encryption algorithm being unsupported, run this instead:

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

The email address just labels the key for your personal reference. When prompted to save your new key, press `<Enter>` to use the default location. When prompted for a passphrase (a password), do not skip this step! Read below for instructions on how to use SSH agent to avoid needing to type your passphrase all the time.

These commands will create two files in your `.ssh` directory located at `$HOME/.ssh` (Mac) or `$Env:USERPROFILE/.ssh` (Windows Powershell): `id_ed25519` and `id_ed25519.pub`, or `id_rsa` and `id_rsa.pub` if you ran the second command. `$HOME` and `$Env:USERPROFILE` are environment variables that refer to `/Users/<username>` on Mac and `C:\Users\<username>` on Windows, respectively, where `<username>` is your account name.

The file ending in `.pub` is your public key and is meant to be copied to servers that you want to access. The other file is your private key and should *never* be shared with anyone.

## Add your public key to GitHub (all platforms)

Login to GitHub. Click your avatar in the upper right corner of the window, and select Settings. Then select SSH and GPG keys on the left. Click the New SSH key button. Give the key a name (any name will do), leave Key type as-is, and copy and paste the contents of your public key file into the text box. You can open the file in a program like Notepad or TextEdit. Or you can print its contents in the Terminal with `type id_ed25519.pub` (Windows) or `cat id_ed25519.pub` (Mac).

## Make sure everything works so far

To check whether you are on the right track, in Terminal, run `ssh -T git@github.com`. If all is well, you will be prompted for your key's passphrase, and then you will get a successful connection message.

## Activate SSH Agent on Windows

If you try using your key to connect to GitHub now, you will be prompted to enter your passphrase every time you connect. SSH Agent will enable you to use your key without needing to enter the passphrase all the time.

Open Services as an administrator (from any user account, search for Services in the search bar and look for Run as administrator). Scroll down to OpenSSH Authentication Agent > right click > Properties. Change the Startup type from Disabled to Automatic. Then open PowerShell as an administrator the same way, and type `Start-Service ssh-agent` to start the agent. You only have to do this once - ssh-agent will start automatically on boot from now on.

## Add your public key to SSH Agent (all platforms)

As promised, if you do this step, you will not have to enter your key's passphrase every time it is used. In Terminal, from your `.ssh` directory run `ssh-add id_ed25519` or `ssh-add id_rsa` depending on which file you generated above. Enter the passphrase when prompted, and you should see an Identity added message.

On macOS, you can also use `ssh-add --apple-use-keychain <private key filename>` to use Apple's Keychain feature. You can check if it worked by opening Keychain Access and searching for a password called `'SSH: <private key filename>'`.

## Make sure it worked

To check whether all this worked, in Terminal, run `ssh -T git@github.com` again. If all is well, you should see the same connection successful message without needing to enter a passphrase.

## Configure Git

Run the following two commands in your Terminal to set your name and your email address. Doing this will ensure that your changes to your project are easily recognizable as coming from you.
```
git config --global user.name "<Your name>"
git config --global user.email "<Your email address>"
```

## Configure VS Code to use GitHub

Go to the Source Control tab in the sidebar and click "Clone Repository" (or, using the Command Palette, run the command "Git: Clone"). Click "Clone from GitHub;" you will be asked to authenticate using your GitHub account. If you have multiple versions of Visual Studio Code installed, make sure to open the authentication link in the right version.

You'll also find some additional extensions useful. Install the Git Graph extension from the extensions marketplace (it's free).

## Next steps

This concludes the installation and configuration guide. There are lots of tutorials and documentation about Git and GitHub online, and there is much more to learn. Visual Studio Code and other integrated development environments like IntelliJ have built in Git support that hides all of Git's complexity behind menus and windows. Including VS Code, some even have GitHub support, which will make using Git even easier. However, after you get started with the basics, it is important to learn what is happening under the hood. One of the better sources of information about Git is the Git Book, found [here](https://git-scm.com/book/en/v2).

Happy coding!
