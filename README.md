# 基本手順

1. リポジトリをクローン(古いansible.cfgは要削除)  
2. ansibleをinstall。古いバージョンだと失敗する可能性アリ  
3. インベントリファイルでIPとパスを変更  
4. SELinux/セキュリティグループ設定  
5. リスタート

# Zabbix-server

1. git cloen  
```
git clone https://github.com/bigmonstershift/Zabbix_By_Ansible.git
```

2. タイムゾーン  
~~OSのタイムゾーンを変更する。~~
※OSのタイムゾーンを変更しなくても、コンフィグで指定していれば自動で変えてくれる。

3. FireWall  

- 10050/tcp
- 10051/tcp
- 80/tcp

加えてサーバ側はSELinuxをOFFにする。
```
setenforce 0
```

4. Ansible  
Ansibleをインストール。
```
yum -y update
yum -y install epel-release
yum -y install ansible
```
展開する場所は下記を参照。
```
cp -r Zabbix_By_Ansible/* /etc/ansible
※ansible.cfgはコピーする必要はありません。
```

5. 各変数  
インベントリファイルにてホストグループ毎に定義。  
変数を展開する時はダブルクォーテーションでくくる。

# Zabbix-agent

1. 権限  
rootユーザにならずにbecomeディレクティブの値をyesにするとsudoが実行できるようになる。

2. 鍵  
ssh用の鍵を以下の場所に格納する。
```
/etc/ansible/.ssh/private.pem
格納ディレクトリ:700
鍵ファイル:600
```

3. FireWall   

- 10050/tcp
- 10051/tcp

4. zabbix_agentd.conf  
インベントファイルの変数を設定する。
```
./hosts
各IPアドレスなどを記入する
```
実行するときは`-C`オプションでテスト出来る。
```
ansible-playbook -i hosts start.yml -C
```

# プレイブック実行後
`http://【ZabbixServerHostAddress】/zabbix`へアクセス。  
初回ログイン時アカパス↓。
```
ユーザ名：Admin
パスワード：zabbix
```
<img width="1440" alt="host" src="https://user-images.githubusercontent.com/53789788/87028164-2d684300-c219-11ea-9b40-6c7358f27daf.png">

