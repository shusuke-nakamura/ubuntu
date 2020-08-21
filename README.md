# ubuntuでローカルの開発環境を作成します。

【前提】
・VirtualBox
・Vagrant
・VScode(Remote Development プライグイン)

【インストール手順】
vagrant up --provision
vagrant ssh-config >> ~/.ssh/config

【git】
git config --global user.name "【ユーザー名】"
git config --global user.email 【メールアドレス】
git config --global core.editor 'vim -c "set fenc=utf-8"'
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
(/home/vagrant/.ssh/id_rsa_github)

vim ~/.ssh/config
Host github
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_github

【rails】
gem install rails -v 6.0.3

【mariadb】
sudo mysql
 update mysql.user set plugin='' where user='root';
 
sudo mysql_secure_installation

【python】
vagrant@vagrant:~/work$ pip list
Package    Version
---------- -------
pip        20.1.1
setuptools 47.1.0
WARNING: You are using pip version 20.1.1; however, version 20.2.2 is available.
You should consider upgrading via the '/home/vagrant/.pyenv/versions/3.8.5/bin/python3.8 -m pip install --upgrade pip' command.
vagrant@vagrant:~/work$ which python
/home/vagrant/.pyenv/shims/python
vagrant@vagrant:~/work$ /home/vagrant/.pyenv/versions/3.8.5/bin/python3.8 -m pip install --upgrade pip
Collecting pip
  Downloading pip-20.2.2-py2.py3-none-any.whl (1.5 MB)
     |████████████████████████████████| 1.5 MB 695 kB/s
Installing collected packages: pip
  Attempting uninstall: pip
    Found existing installation: pip 20.1.1
    Uninstalling pip-20.1.1:
      Successfully uninstalled pip-20.1.1
Successfully installed pip-20.2.2
vagrant@vagrant:~/work$ pip list
Package    Version
---------- -------
pip        20.2.2
setuptools 47.1.0
vagrant@vagrant:~/work$ pip list
Package           Version
----------------- -------
astroid           2.4.2
isort             5.4.2
lazy-object-proxy 1.4.3
mccabe            0.6.1
pip               20.2.2
pylint            2.6.0
setuptools        47.1.0
six               1.15.0
toml              0.10.1
wrapt             1.12.1


# ubuntuでRails環境を作成します(Docker)

【前提】
・VirtualBox
・Vagrant
上記、2点がインストール済みであること

【インストール手順】
【管理者権限でコマンドプロンプトを起動 --- START】
・vagrant up --provision
・vagrant halt
・cd C:\Windows\system32
・fsutil behavior set SymlinkEvaluation L2L:1 R2R:1 L2R:1 R2L:1
・"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" list vms
・"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" setextradata [VB名] VBoxInternal2/SharedFoldersEnableSymlinksCreate/vagrant 1
・"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" getextradata [VB名] enumerate
・vagrant up
【管理者権限でコマンドプロンプトを起動 --- END】

【SSH接続を行て実行】
・docker-compose run web rails new . --force --no-deps --database=postgresql
・docker-compose build
・datebase.ymlを修正
・docker-compose run web rake db:create
・docker-compose up

【Rails画面を開く】
http://192.168.33.10:3000/

【備考】
docker-compose up -d
(デーモンとして起動する)
docker-compose stop
(デーモンを停止する)
docker-compose exec web bash
(Webサーバに接続する)
docker-compose exec db bash
(DBサーバに接続する)
