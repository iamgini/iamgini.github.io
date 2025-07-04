---
layout: post
title: How to Configure and Use GitHub Jekyll Local Environment
author: gini
categories: [ jekyll ]
image: "assets/images/2020/infrastructure.jpg"
tags: [web, jekyll]
permalink: jekyll
featured: false
hidden: false
titleshort: jekyll
---

# Install Jekyll & Bundler

Refer the documentation based on your Operating System.

[Ubuntu](https://jekyllrb.com/docs/installation/ubuntu/) | [MacOS](https://jekyllrb.com/docs/installation/macos/) | [Linux](https://jekyllrb.com/docs/installation/other-linux/) | [Windows](https://jekyllrb.com/docs/installation/windows/)

# Instructions for MacOS

## Install Command Line Tools

```shell
xcode-select --install
```

set SDKROOT (only macOS Catalina or later)Permalink

```shell
export SDKROOT=$(xcrun --show-sdk-path)
```

## Install Ruby

Jekyll requires Ruby v2.5.0 or higher but MacOS Big Sur 11.x is already installed with Ruby 2.6.3. Check the version and confirm.

```shell
% ruby -v
ruby 3.0.2p107 (2021-07-07 revision 0db68f0233) [x86_64-darwin20]
```

If you’re running a previous version of macOS, you’ll have to install a newer version of Ruby.

## Install Ruby using `rbenv` (Multiple Ruby versions)

This is very useful when you need to be able to run a given Ruby version on a project.

```shell
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install rbenv and ruby-build
brew install rbenv

# Set up rbenv integration with your shell
rbenv init

# Check your installation
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/main/bin/rbenv-doctor | bash
```

Re-Open the terminal and the version

```shell
## Install 3.0.0
$ rbenv install 3.0.0
$ rbenv global 3.0.0

$ ruby -v
ruby 3.0.0p0 (2020-12-25 revision 95aff21468)

## Install 2.7.0
$rbenv install 2.7.0

## set global version
$ rbenv global 2.7.0
$ ruby -v
ruby 3.0.0p0 (2020-12-25 revision 95aff21468)

## check versions
% rbenv versions
* system (set by /Users/gini/.rbenv/version)
  2.7.0
  3.0.0

## set local version
% rbenv local 2.7.0

## show current local version
% rbenv version
2.7.0 (set by /Users/gini/codes/ginigangadharan.github.io/.ruby-version)
gini@greenmango ginigangadharan.github.io %

```

## Install Jekyll

(Use localinstall rathr than Global Install)

```shell
gem install --user-install bundler jekyll
## --[no-]user-install          Install in user's home directory instead
```

Append your path file with the following, replacing the X.X with the first two digits of your Ruby version:

```shell
# If you're using Zsh
echo 'export PATH="$HOME/.gem/ruby/X.X.0/bin:$PATH"' >> ~/.zshrc

# If you're using Bash
echo 'export PATH="$HOME/.gem/ruby/X.X.0/bin:$PATH"' >> ~/.bash_profile

# Unsure which shell you are using? Type
echo $SHELL
```

Check that GEM PATHS: points to your home directory:

```shell
gem env
```

# Instructions for Ubuntu
## Install packages


```bash
## Ubuntu
sudo apt-get install ruby-full build-essential zlib1g-dev

## RHEL8/CentOS8
sudo dnf install ruby ruby-devel
sudo dnf group install "Development Tools"
```


## Configure gem path
```
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## Install jekyll & bundler
```
gem install --user-install bundler jekyll
```

# Test your site
Goto your project directory
```
bundle exec jekyll serve
```

## Benchmarking your Jekyll website

```shell
$ bundle exec jekyll build --profile
```

# Updating the GitHub Pages gem
If the github-pages gem on your computer is out of date with the github-pages gem on the GitHub Pages server, your site may look different when built locally than when published on GitHub. To avoid this, regularly update the github-pages gem on your computer.

- If you installed Bundler, run bundle update github-pages.
- If you don't have Bundler installed, run gem update github-pages.


# If no Gemfile

```
bundle init
bundle add jekyll
bundle install --path vendor/bundle
```

## Jekyll-Admin - GUI Editor


### Add Plugin in `Gemfile`

```
.
.
group :jekyll_plugins do
    .
    .
    .
    gem 'jekyll-admin'
end
```

### Add configuration in `_config.yaml`

```yaml
jekyll_admin:
  homepage: "posts"
  hidden_links:
    #- posts
    #- pages
    #- staticfiles
    - datafiles
    #- configuration
```

Refer [Jekyll Admin - Documentation](https://jekyll.github.io/jekyll-admin/) and [GitHub](https://github.com/jekyll/jekyll-admin) for details.