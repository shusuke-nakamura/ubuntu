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
