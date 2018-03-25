
## 環境
```
user@ubuntu:~/git/ansible-practice$ uname -a
Linux ubuntu 4.4.0-116-generic #140-Ubuntu SMP Mon Feb 12 21:23:04 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
user@ubuntu:~/git/ansible-practice$ 
```
---

## gitインストール　設定
```
sudo apt-get -y install git

git config --global user.name test
git config --global user.email test@test
```
---

## python導入確認　追加する場合
```
python --version

sudo apt-get -y install python
sudo apt-get -y install python-pip libssl-dev
```

---

## ansibleのサンプルを取得（書籍参考箇所）
```
https://github.com/kota-row/ansible-practice.git
git clone https://github.com/kota-row/ansible-practice.git
```

---

## ansibleインストール
```
user@ubuntu:~/git/ansible-practice$ cat requirements.txt 
ansible==2.2
pywinrm
user@ubuntu:~/git/ansible-practice$ 

pip install -r requirements.txt --user

pip install ansible==2.2 pywinrm --user

ansible --version
```

---

## 仮想環境ツール準備
```
http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html?ssSourceSiteId=otnjp
curl https://download.virtualbox.org/virtualbox/5.2.6/virtualbox-5.2_5.2.6-120293~Ubuntu~xenial_amd64.deb \
 -o virtualbox-5.2_5.2.6-120293~Ubuntu~xenial_amd64.deb
sudo dpkg -i virtualbox-5.2_5.2.6-120293~Ubuntu~xenial_amd64.deb

https://www.vagrantup.com/downloads.html
curl https://releases.hashicorp.com/vagrant/2.0.2/vagrant_2.0.2_x86_64.deb?_ga=2.105198724.609176761.1519527045-355190992.1518935519 \
 -o vagrant_2.0.2_x86_64.deb
sudo dpkg -i vagrant_2.0.2_x86_64.deb

sudo apt-get -f install

VBoxManage --version
Vargrant --version

https://cloud-images.ubuntu.com/xenial/current

vmwareのVT-X機能を有効化し起動をしなおす。
```

---

## 仮想環境構成ファイル
```
user@ubuntu:~/git/ansible-practice$ cat Vagrantfile 
#!/usr/bin/ruby

Vagrant.configure('2') do |config|
  config.vm.box = "ubuntu/xenial64"

  {
    'ubuntu01' => '192.168.33.10',
    'ubuntu02' => '192.168.33.20'
  }.each do |name, address|
    config.vm.define name do |host|
      host.vm.hostname = name
      host.vm.network :private_network, ip: address
    end
  end

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024", "--cpus", "2", "--ioapic", "on"]
  end

  config.vm.provision 'shell', inline: <<-SCRIPT
    apt-get update
    apt-get -y install python aptitude
  SCRIPT

end
user@ubuntu:~/git/ansible-practice$ 
user@ubuntu:~/git/ansible-practice$ cat ansible.cfg 
[defaults]
inventory = hosts
#remote_user = ubuntu
remote_user = vagrant
host_key_checking = False
private_key_file = ~/git/.vagrant/machines/{{ inventory_hostname }}/virtualbox/private_key
# roles_pathは書籍本文のサンプルコードに記載がありませんが、playbookの配置パスの都合上指定が必要なため追記しています。
# 公式サイト( http://gihyo.jp/book/2017/978-4-7741-8885-0 )の正誤表をご確認ください
roles_path = ~/git/roles

[privilege_escalation]
become = True
user@ubuntu:~/git/ansible-practice$ 
```
## 環境構築
```
vagrant status
vagrant up
vagrant status

vagrant ssh ubuntu01
```

---
