# ubuntu��Rails�����쐬���܂�

�y�O��z
�EVirtualBox
�EVagrant
��L�A2�_���C���X�g�[���ς݂ł��邱��

�y�C���X�g�[���菇�z
�y�Ǘ��Ҍ����ŃR�}���h�v�����v�g���N�� --- START�z
�Evagrant up --provision
�Evagrant halt
�Ecd C:\Windows\system32
�Efsutil behavior set SymlinkEvaluation L2L:1 R2R:1 L2R:1 R2L:1
�E"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" list vms
�E"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" setextradata [VB��] VBoxInternal2/SharedFoldersEnableSymlinksCreate/vagrant 1
�E"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" getextradata [VB��] enumerate
�Evagrant up
�y�Ǘ��Ҍ����ŃR�}���h�v�����v�g���N�� --- END�z

�ySSH�ڑ����s�Ď��s�z
�Edocker-compose run web rails new . --force --no-deps --database=postgresql
�Edocker-compose build
�Edatebase.yml���C��
�Edocker-compose run web rake db:create
�Edocker-compose up

�yRails��ʂ��J���z
http://192.168.33.10:3000/

�y���l�z
docker-compose up -d
(�f�[�����Ƃ��ċN������)
docker-compose stop
(�f�[�������~����)
docker-compose exec web bash
(Web�T�[�o�ɐڑ�����)
docker-compose exec db bash
(DB�T�[�o�ɐڑ�����)
