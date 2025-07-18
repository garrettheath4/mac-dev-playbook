<img src="https://raw.githubusercontent.com/geerlingguy/mac-dev-playbook/master/files/Mac-Dev-Playbook-Logo.png" width="250" height="156" alt="Mac Dev Playbook Logo" />

# Mac Development Ansible Playbook

[![CI][badge-gh-actions]][link-gh-actions]

This playbook installs and configures most of the software I use on my Mac for web and software development. Some things in macOS are slightly difficult to automate, so I still have a few manual installation steps, but at least it's all documented here.


## Installation

  1. Ensure Apple's command line tools are installed:

        xcode-select --install

  2. Install [Homebrew](https://brew.sh/) by downloading
     [latest release installer](https://github.com/Homebrew/brew/releases/latest) or running:

        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

  3. [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html):

        brew install ansible

  4. Clone or download this repository to your local drive.

        git clone https://github.com/garrettheath4/dotfiles-mac.git

  5. Run `ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.
  6. Run `ansible-playbook main.yml --ask-become-pass` inside this directory. Enter your macOS account password when prompted for the 'BECOME' password.

> Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run `brew doctor` to see if this is the case.


### Use with a remote Mac

You can use this playbook to manage other Macs as well; the playbook doesn't even need to be run from a Mac at all! If you want to manage a remote Mac, either another Mac on your network, or a hosted Mac like the ones from [MacStadium](https://www.macstadium.com), you just need to make sure you can connect to it with SSH:

  1. (On the Mac you want to connect to:) Go to System Preferences > Sharing.
  2. Enable 'Remote Login'.

> You can also enable remote login on the command line:
>
>     sudo systemsetup -setremotelogin on

Then edit the `inventory` file in this repository and change the line that starts with `127.0.0.1` to:

```
[ip address or hostname of mac]  ansible_user=[mac ssh username]
```

If you need to supply an SSH password (if you don't use SSH keys), make sure to pass the `--ask-pass` parameter to the `ansible-playbook` command.


### Running a specific set of tagged tasks

You can filter which part of the provisioning process to run by specifying a set of tags using `ansible-playbook`'s `--tags` flag. The tags available are `dotfiles`, `homebrew`, `mas`, `extra-packages`, `osx`, and `dock`.

    ansible-playbook main.yml -K --tags "dotfiles,homebrew"


## Overriding Defaults

Not everyone's development environment and preferred software configuration is the same.

You can override any of the defaults configured in `default.config.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

```yaml
homebrew_installed_packages:
  - cowsay
  - git
  - go

mas_installed_apps:
  - { id: 443987910, name: "1Password" }
  - { id: 498486288, name: "Quick Resizer" }
  - { id: 557168941, name: "Tweetbot" }
  - { id: 497799835, name: "Xcode" }

composer_packages:
  - name: hirak/prestissimo
  - name: drush/drush
    version: '^8.1'

gem_packages:
  - name: bundler
    state: latest

npm_packages:
  - name: webpack

pip_packages:
  - name: mkdocs

configure_dock: true
dockitems_remove:
  - Launchpad
  - TV
dockitems_persist:
  - name: "Sublime Text"
    path: "/Applications/Sublime Text.app/"
    pos: 5
```

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.


## Included Applications / Configuration (Default)

Applications (installed from Mac App Store):

 - [1Keyboard](https://apps.apple.com/us/app/id766939888)
 - [Deliveries: a package tracker](https://apps.apple.com/us/app/id290986013)
 - [Drafts](https://apps.apple.com/us/app/id1435957248)
 - [Messenger](https://apps.apple.com/us/app/id1480068668)
 - [Slack for Desktop](https://apps.apple.com/us/app/id803453959)
 - [Table Tool](https://apps.apple.com/us/app/id1122008420)
 - [Todoist: To-Do List & Tasks](https://apps.apple.com/us/app/id585829637)
 - [WireGuard](https://apps.apple.com/us/app/id1451685025)
 - [iA Writer](https://apps.apple.com/us/app/id775737590)
 - [Dark Reader for Safari](https://apps.apple.com/us/app/id1438243180)
 - [Ka-Block!](https://apps.apple.com/us/app/id1335413823)
 - [Tampermonkey](https://apps.apple.com/us/app/id1482490089)
 - [Vimari](https://apps.apple.com/us/app/id1480933944)

Applications (installed with Homebrew Cask):

  - [1Password 8](https://formulae.brew.sh/cask/1password)
  - [Bartender](https://formulae.brew.sh/cask/bartender)
  - [Brave](https://formulae.brew.sh/cask/brave-browser)
  - [Discord](https://formulae.brew.sh/cask/discord)
  - [Docker](https://formulae.brew.sh/cask/docker)
  - [Insync](https://formulae.brew.sh/cask/insync)
  - [iTerm2](https://formulae.brew.sh/cask/iterm2)
  - [JetBrains Toolbox](https://formulae.brew.sh/cask/jetbrains-toolbox)
  - [Kindle](https://formulae.brew.sh/cask/kindle)
  - [MacDown](https://formulae.brew.sh/cask/macdown)
  - [MacVim](https://formulae.brew.sh/cask/macvim)
  - [MonitorControl](https://formulae.brew.sh/cask/monitorcontrol)
  - [Moom](https://formulae.brew.sh/cask/moom)
  - [Raycast](https://www.raycast.com/)
  - [Spotify](https://formulae.brew.sh/cask/spotify)
  - [Tuple](https://formulae.brew.sh/cask/tuple)
  - [xbar](https://formulae.brew.sh/cask/xbar)

Packages (installed with Homebrew):

  - [agg](https://github.com/asciinema/agg)
  - [asciinema](https://asciinema.org)
  - bash-completion
  - coreutils
  - ffmpeg
  - [fzf](https://github.com/junegunn/fzf) (fuzzy finder)
  - git
  - [moreutils](https://joeyh.name/code/moreutils/) (`vipe` and other commands)
  - node
  - nvm
  - python3
  - reattach-to-user-namespace
  - ruby
  - [shellcheck](https://github.com/koalaman/shellcheck)
  - [the_silver_searcher](https://github.com/ggreer/the_silver_searcher) (`ag` command)
  - tmux
  - wget
  - youtube-dl
  - [zoxide](https://github.com/ajeetdsouza/zoxide)

My [dotfiles](https://github.com/geerlingguy/dotfiles) are also installed into the current user's home directory, including the `.osx` dotfile for configuring many aspects of macOS for better performance and ease of use. You can disable dotfiles management by setting `configure_dotfiles: no` in your configuration.

Finally, there are a few other preferences and settings added on for various apps and services.

## Full / From-scratch setup guide

Since I've used this playbook to set up something like 20 different Macs, I decided to write up a full 100% from-scratch install for my own reference (everyone's particular install will be slightly different).

You can see my full from-scratch setup document here: [full-mac-setup.md](full-mac-setup.md).


## Testing the Playbook

Many people have asked me if I often wipe my entire workstation and start from scratch just to test changes to the playbook. Nope! This project is [continuously tested on GitHub Actions' macOS infrastructure](https://github.com/geerlingguy/mac-dev-playbook/actions?query=workflow%3ACI).

You can also run macOS itself inside a VM, for at least some of the required testing (App Store apps and some proprietary software might not install properly). I currently recommend:

  - [UTM](https://mac.getutm.app)
  - [Tart](https://github.com/cirruslabs/tart)


## Ansible for DevOps

Check out [Ansible for DevOps](https://www.ansiblefordevops.com/), which teaches you how to automate almost anything with Ansible.


## Author

This project was created by [Jeff Geerling](https://www.jeffgeerling.com/) (originally inspired by [MWGriffin/ansible-playbooks](https://github.com/MWGriffin/ansible-playbooks)).


<!-- Links -->
[badge-gh-actions]: https://github.com/geerlingguy/mac-dev-playbook/workflows/CI/badge.svg?event=push
[link-gh-actions]: https://github.com/geerlingguy/mac-dev-playbook/actions?query=workflow%3ACI
