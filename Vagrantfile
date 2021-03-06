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
  config.vm.box_version = "202010.24.0"

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
  # config.vm.network "forwarded_port", guest: 22, host: 1234, id: 'ssh'
  config.vm.network "forwarded_port", guest: 80, host: 10080, id: 'apache'
  config.vm.network "forwarded_port", guest: 3000, host: 13000, id: 'rails'
  
  #config.ssh.insert_key = false
  #config.ssh.username = 'vagrant'
  #config.ssh.password = 'vagrant'

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
    vb.gui = false
    vb.memory = "1024"
    vb.cpus = "2"
    vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/vagrant", "1"]
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

# Gitクライアントのインストール
sudo apt-get install -y tig

# expectのインストール
sudo apt-get install -y expect

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
# apache2のインストール
APACHE2_INSTALLED=`sudo dpkg -l apache2 | grep apache2 | wc -l`
if [ $APACHE2_INSTALLED -eq 0 ]
then
    sudo apt-get install -y apache2
fi
# ドキュメントルート(作業領域)を作成
if [ ! -d ~/www/html ]
then
    mkdir -p ~/www/html
fi
# シンボリックリンクを作成
if [ -d /var/www/html ]
then
    sudo rm -rf /var/www/html
    sudo ln -s ~/www/html /var/www/html
fi
######################################################################
# sendmailのインストール
SEND_MAIL_INSTALLED=`sudo dpkg -l | grep sendmail | wc -l`
if [ $SEND_MAIL_INSTALLED -eq 0 ]
then
    sudo apt-get install -y sendmail
fi
######################################################################
# ruby
# 必要なツール、パッケージの導入
sudo apt-get install -y autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm6 libgdbm-dev libdb-dev imagemagick sqlite3 libsqlite3-dev
RUBY_INSTALL_VERSION=2.7.2
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
  RUBY_INSTALLED_VERSION=`rbenv versions --bare | grep $RUBY_INSTALL_VERSION | wc -l`
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
PYTHON_INSTALL_VERSION=anaconda3-2020.07
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
# 必要なツール、パッケージの導入
sudo apt-get install -y pkg-config libxml2-dev libkrb5-dev libcurl4-openssl-dev libpng-dev libjpeg-dev libonig-dev libtidy-dev libxslt1-dev libzip-dev
PHP_INSTALLED=`sudo dpkg -l | grep php | wc -l`
if [ $PHP_INSTALLED -eq 0 ]
then
    sudo apt-get install -y  php libapache2-mod-php php-gd php-xml php-cli php-mbstring php-soap php-xmlrpc php-zip php-bcmath php7.4-mysql
    if [ ! -f /vagrant/setting_files/php/default/apache2/php.ini ]
    then
        sudo cp -p /etc/php/7.4/apache2/php.ini /vagrant/setting_files/php/default/apache2/php.ini
    fi
    if [ ! -f /vagrant/setting_files/php/default/cli/php.ini ]
    then
        sudo cp -p /etc/php/7.4/cli/php.ini /vagrant/setting_files/php/default/cli/php.ini
    fi
    sudo cp /vagrant/setting_files/php/202008/apache2/php.ini /etc/php/7.4/apache2/php.ini
    sudo cp /vagrant/setting_files/php/202008/cli/php.ini /etc/php/7.4/cli/php.ini

fi
######################################################################
# node.jsのインストール
# nodebrewのインストール
NODE_JS_INSTALL_VERSION=v14.15.0
if [ ! -d ~/.nodebrew ]
then
    curl -L git.io/nodebrew | perl - setup
    echo 'export PATH="$HOME/.nodebrew/current/bin:$PATH"' >> ~/.bashrc
fi
export PATH="$HOME/.nodebrew/current/bin:$PATH"
NODE_JS_INSTALLED_VERSION=`nodebrew list | grep $NODE_JS_INSTALL_VERSION | wc -l`
if [ $NODE_JS_INSTALLED_VERSION -eq 0 ]
then
    nodebrew install-binary $NODE_JS_INSTALL_VERSION
    nodebrew use $NODE_JS_INSTALL_VERSION
fi
######################################################################
# yarnのインストール
YARN_INSTALLED=`npm ls -g | grep yarn | wc -l`
if [ $YARN_INSTALLED -eq 0 ]
then
    npm install -g yarn
fi
######################################################################
# mongodbのインストール
# gnupg
GNUPG_INSTALLED=`sudo dpkg -l gnupg | grep gnupg | wc -l`
if [ $GNUPG_INSTALLED -eq 0 ]
then
    sudo apt-get install -y gnupg
fi
if [ ! -f /etc/apt/sources.list.d/mongodb-org-4.4.list ]
then
    wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
    sudo apt-get update
fi
MONGODB_INSTALLED=`sudo dpkg -l mongodb-org | grep mongodb | wc -l`
if [ $MONGODB_INSTALLED -eq 0 ]
then
    sudo apt-get install -y mongodb-org
    sudo systemctl daemon-reload
    sudo systemctl start mongod
    sudo systemctl enable mongod
fi
######################################################################
# mariadbのインストール
# https://qiita.com/mwatanabe@github/items/7e9a40d31bc27ab9d901
# https://qiita.com/nanbuwks/items/c98c51744bd0f72a7087
# unix_socket の解除の仕方
# update mysql.user set plugin='' where user='root';
# mysql_secure_installation
MARIADB_INSTALLED=`sudo dpkg -l mariadb-server | grep mariadb-server | wc -l`
if [ $MARIADB_INSTALLED -eq 0 ]
then
    sudo apt-get install -y mariadb-server
fi
######################################################################
# xdebugのインストール
XDEBUG_INSTALLED=`sudo dpkg -l | grep php-xdebug | wc -l`
if [ $XDEBUG_INSTALLED -eq 0 ]
then
    sudo apt-get install -y php-xdebug
fi
XDEBUG_APACHE=`grep xdebug /etc/php/7.4/apache2/php.ini | wc -l`
XDEBUG_CLI=`grep xdebug /etc/php/7.4/cli/php.ini | wc -l`

if [ $XDEBUG_APACHE -eq 0 ]
then
    sudo cp /vagrant/setting_files/php/202008/apache2/xdebug/php.ini /etc/php/7.4/apache2/php.ini
fi

if [ $XDEBUG_CLI -eq 0 ]
then
    sudo cp /vagrant/setting_files/php/202008/cli/xdebug/php.ini /etc/php/7.4/cli/php.ini
fi
######################################################################
# PHP_INI_SEND_MAIL
SEND_MAIL_APACHE=`grep ";sendmail_path" /etc/php/7.4/apache2/php.ini | wc -l`
SEND_MAIL_CLI=`grep ";sendmail_path" /etc/php/7.4/cli/php.ini | wc -l`

if [ $SEND_MAIL_APACHE -eq 1 ]
then
    sudo cp /vagrant/setting_files/php/202008/apache2/sendmail/php.ini /etc/php/7.4/apache2/php.ini
fi

if [ $SEND_MAIL_CLI -eq 1 ]
then
    sudo cp /vagrant/setting_files/php/202008/cli/sendmail/php.ini /etc/php/7.4/cli/php.ini
fi

######################################################################
# composerのインストール
# https://mebee.info/2020/06/02/post-10844/
if [ ! -f /usr/local/bin/composer ]
then
   curl -sS https://getcomposer.org/installer | php
   sudo mv composer.phar /usr/local/bin/composer
   sudo chmod +x /usr/local/bin/composer
   sudo chown root:root /usr/local/bin/composer
   source ~/.bashrc
fi
######################################################################
# Open JDKのインストール
######################################################################
OPEN_JDK_INSTALLED=`sudo dpkg -l | grep openjdk-11-jdk | wc -l`
if [ $OPEN_JDK_INSTALLED -eq 0 ]
then
   sudo apt-get install -y openjdk-11-jdk
fi
# JAVA_HOMEの設定
ENV_JAVA_HOME=`env | grep JAVA_HOME | wc -l`
BASHRC_JAVA_HOME=`grep JAVA_HOME ~/.bashrc | wc -l`
if [ $ENV_JAVA_HOME -eq 0 ] && [ $BASHRC_JAVA_HOME -eq 0 ]
then
    echo 'export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64' >> ~/.bashrc
    source ~/.bashrc
fi

######################################################################
# gradleのインストール
######################################################################
GRADLE_INSTALLED=`sudo dpkg -l | grep gradle | wc -l`
if [ $GRADLE_INSTALLED -eq 0 ]
then
    sudo apt-get install -y gradle
fi

######################################################################
# graphvizのインストール
######################################################################
GRAPHVIZ_INSTALLED=`sudo dpkg -l | grep graphviz | wc -l`
if [ $GRAPHVIZ_INSTALLED -eq 0 ]
then
   sudo apt-get install -y graphviz
fi


######################################################################
# Visual Studio コードは、この大きなワークスペースでのファイルの変更を監視できません
# https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc
# cat /proc/sys/fs/inotify/max_user_watches ⇒ 現在の設定値
# fs.inotify.max_user_watches=524288 ⇒ MAX値に設定
SET_MAX_USER_WATCHES=`sudo grep fs.inotify.max_user_watches /etc/sysctl.conf | wc -l`
if [ $SET_MAX_USER_WATCHES -eq 0 ]
then
    sudo cp -p /etc/sysctl.conf /vagrant/sysctl.conf
    sudo sh -c 'echo fs.inotify.max_user_watches=524288 >> /etc/sysctl.conf'
    sudo sysctl -p
fi

SCRIPT
