# 基本手順

1. リポジトリをクローン(古いansible.cfgは要削除)  
2. ansibleをinstall。古いバージョンだと失敗する可能性アリ  
3. インベントリファイルでIPとパスを変更  
4. SELinux/セキュリティグループ設定  
5. リスタート

# Zabbix-server

1. git cloen  
```
$ git clone -b jinja2 https://github.com/ahegaowpeace/Zabbix_By_Ansible.git
```

2. タイムゾーン  
OSのタイムゾーン変更も忘れないで
※OSのタイムゾーンを変更しなくても、コンフィグで指定していれば自動で変えてくれるらしい。

3. FireWall  
firewalldの設定は自動化されていない。awsでやるならセキュリティグループの設定が必要になる。  

- 10050/tcp
- 10051/tcp
- 80/tcp

加えてサーバ側はSELinuxをOFFにする。
```
# setenforce 0
```

4. Ansible  
Ansibleをインストール。
```
# yum -y install epel-release
# yum -y install ansible
```
展開する場所は下記を参照。
```
sudo cp -r Zabbix_By_Ansible/* /etc/ansible
※ansible.cfg以外を上書きでコピーしたらいいと思います。
```

5. 各変数  
インベントリにてホストグループ毎に定義  
変数を展開する時はダブルクォーテーションでくくる

# Zabbix-agent

1. 権限  
rootユーザにならずにbecomeディレクティブの値をyesにするとsudoが実行できるようになる。

2. 鍵  
ssh用の鍵を以下の場所に格納する。
```
.ssh/private.pem
格納ディレクトリ:700
鍵ファイル:600
```

3. zabbix_agentd.conf  
インベントファイルの変数を設定する
```
./hosts
各IPアドレスなどを記入する
```
※現在はJinja2によりインベントリファイルにIPとパスを記載するだけでよい。
実行するときはチェックオプションでテストするのも忘れず
```
# ansible-playbook -i hosts start.yml -C
```

