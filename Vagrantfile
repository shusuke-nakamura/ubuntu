# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/ubuntu-20.04"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  config.vm.network "forwarded_port", guest: 22, host: 1234, id: 'ssh'
  
  config.ssh.insert_key = false
  config.ssh.username = 'vagrant'
  config.ssh.password = 'vagrant'

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/.", "1"]
  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
  config.vm.provision :shell, privileged: false, inline: $script
end
$script = <<SCRIPT
######################################################################
# Docker リポジトリの登録

# パッケージの更新
sudo apt-get update

# 日本語環境の作成
sudo locale-gen ja_JP.UTF-8

# 起動時に日本語設定で起動する
LOCALE_SETTING=`grep ja_JP.UTF-8 ~/.profile | wc -l`
if [ $LOCALE_SETTING -eq 0 ]
then
    echo export LANG=ja_JP.UTF-8 >> ~/.profile
fi

# デフォルトのタイムゾーンUTC ⇒ JSTに変更する
sudo timedatectl set-timezone Asia/Tokyo

# マニュアルを日本語化
sudo apt-get install -y manpages-ja manpages-ja-dev

# Gitのインストール
sudo apt-get install -y git

# HTTPS利用のためのパッケージをインストール
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common

# Docker公式の GPG を追加
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# リポジトリの設定
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

######################################################################
# Docker CE のインストール
# （https://docs.docker.com/install/linux/docker-ce/ubuntu/）

# パッケージの更新
sudo apt-get update

# Docker CE のインストール
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

######################################################################
# Docker Compose のインストール
# （https://docs.docker.com/compose/install）

# Docker compose のダウンロード
# ※適宜 https://github.com/docker/compose/releases の最新バージョンへ書き換えてください
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 実行権限の付与
sudo chmod +x /usr/local/bin/docker-compose

######################################################################
# vagrant ユーザーを docker グループへ追加

sudo usermod -aG docker vagrant

######################################################################
# ruby
# 必要なツール、パッケージの導入
sudo apt-get install -y autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm6 libgdbm-dev libdb-dev
RUBY_INSTALL_VERSION=2.7.1
# rbenvのインストール
if  [ ! -d ~/.rbenv ]
then
    git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
    git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
    # rbenv の環境設定
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(rbenv init -)"' >> ~/.bashrc
fi
if [ -e ~/.rbenv/bin/rbenv ]
then
  export PATH="/home/vagrant/.rbenv/bin:$PATH"
  eval "$(rbenv init -)"
  # rubyのインストール
  RUBY_INSTALLED_VERSION=`rbenv versions | grep $RUBY_INSTALL_VERSION | wc -l`
  if [ $RUBY_INSTALLED_VERSION -eq 0 ]
  then
      rbenv install $RUBY_INSTALL_VERSION
      rbenv global $RUBY_INSTALL_VERSION
      rbenv rehash
  fi
fi
######################################################################
# python
# 必要なツール、パッケージの導入
sudo apt-get install -y build-essential libffi-dev libssl-dev zlib1g-dev liblzma-dev libbz2-dev libreadline-dev libsqlite3-dev
PYTHON_INSTALL_VERSION=3.8.5
# pyenvのインストール
if [ ! -d ~/.pyenv ]
then
    git clone https://github.com/pyenv/pyenv.git ~/.pyenv
    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(pyenv init -)"' >> ~/.bashrc
fi
if [ -e ~/.pyenv/bin/pyenv ]
then
    export PYENV_ROOT="$HOME/.pyenv"
    export PATH="$PYENV_ROOT/bin:$PATH"
    eval "$(pyenv init -)"
    # pythonのインストール
    PYTHON_INSTALLED_VERSION=`pyenv versions | grep $PYTHON_INSTALL_VERSION | wc -l`
    if [ $PYTHON_INSTALLED_VERSION -eq 0 ]
    then
        pyenv install $PYTHON_INSTALL_VERSION
        pyenv global $PYTHON_INSTALL_VERSION
    fi
fi
######################################################################
# php
# phpenvのインストール
# 必要なツール、パッケージの導入
sudo apt-get install -y pkg-config libxml2-dev libkrb5-dev libcurl4-openssl-dev libpng-dev libjpeg-dev libonig-dev libtidy-dev libxslt1-dev libzip-dev
PHP_INSTALL_VERSION=7.4.8
if [ ! -d ~/.phpenv ]
then
    git clone https://github.com/phpenv/phpenv.git ~/.phpenv
    git clone https://github.com/php-build/php-build.git ~/.phpenv/plugins/php-build
    echo 'export PATH="$HOME/.phpenv/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(phpenv init -)"' >> ~/.bashrc
    sudo sh ~/.phpenv/plugins/php-build/install.sh
fi
if [ -d ~/.phpenv/plugins/php-build ]
then
    export PATH="$HOME/.phpenv/bin:$PATH"
    eval "$(phpenv init -)"
    # phpのインストール
    PHP_INSTALLED_VERSION=`phpenv versions | grep $PHP_INSTALL_VERSION | wc -l`
    if [ $PHP_INSTALLED_VERSION -eq 0 ]
    then
        phpenv install $PHP_INSTALL_VERSION
        phpenv global $PHP_INSTALL_VERSION
    fi
fi
######################################################################
# node.jsのインストール
# nodebrewのインストール
NODE_JS_INSTALL_VERSION=v12.18.3
if [ ! -d ~/.nodebrew ]
then
    curl -L git.io/nodebrew | perl - setup
    echo 'export PATH="$HOME/.nodebrew/current/bin:$PATH"' >> ~/.bashrc
fi
export PATH="$HOME/.nodebrew/current/bin:$PATH"
NODE_JS_INSTALLED_VERSION=`nodebrew list | grep v12.18.3 | wc -l`
if [ $NODE_JS_INSTALLED_VERSION -eq 0 ]
then
    nodebrew install-binary $NODE_JS_INSTALL_VERSION
    nodebrew use $NODE_JS_INSTALL_VERSION
fi
SCRIPT
