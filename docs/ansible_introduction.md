
# 環境
```
仮想環境:VMware Workstatison 12 Player
OS:Ubuntu 16.04 LTS
ansible:2.5.0
Vagrant:2.0.2
python:2.7.12
VBoxManage:5.2.6r120293

```
---

# 手順

## 環境準備
```
以下サイトよりインストール
VMwareの準備　
https://www.vmware.com/jp/products/workstation-player/workstation-player-evaluation.htmlhttps://www.ubuntulinux.jp/News/ubuntu1604-ja-remix

Ubuntu 準備
https://www.ubuntulinux.jp/News/ubuntu1604-ja-remix

VirtualBOX 準備
http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html?ssSourceSiteId=otnjp

Vagrant 準備
https://www.vagrantup.com/downloads.html

```
## gitインストール　設定
```
$ sudo apt-get -y install git
$ git config --global user.name test
$ git config --global user.email test@test
```
---

## python導入確認　追加する場合
```
$ python --version
$ sudo apt-get -y install python
$ sudo apt-get -y install python-pip libssl-dev
```

---

## ansibleのサンプルを取得
```
$ cd ~/
$ git clone https://github.com/twomidle/ansible
$ cd ~/ansible
```

---

## ansibleインストール
```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
$ ansible --version
ansible 2.5.0
～～
$ 

```

---

## 仮想環境ツール準備
```
curl https://releases.hashicorp.com/vagrant/2.0.2/vagrant_2.0.2_x86_64.deb \
 -o vagrant_2.0.2_x86_64.deb
sudo dpkg -i vagrant_2.0.2_x86_64.deb

VBoxManage --version

・virtual box 導入時に依存ファイルが不足しているため、以下のコマンドを実行
sudo apt-get install libsdl1.2debian

curl https://download.virtualbox.org/virtualbox/5.2.6/virtualbox-5.2_5.2.6-120293~Ubuntu~xenial_amd64.deb \
 -o virtualbox-5.2_5.2.6-120293~Ubuntu~xenial_amd64.deb
sudo dpkg -i virtualbox-5.2_5.2.6-120293~Ubuntu~xenial_amd64.deb

vagrant --version

・注意
vmwareのデフォルト設定でVT-X機能が無効かされているため、仮想環境の作成に失敗。
vmwareを一旦停止し、オプション画面にて有効化し起動をしなおす。
設定変更箇所は以下のチェックボックスにチェックを入れ、ubuntuを起動する。
　仮想マシン設定の編集
　ハードウェア-プロセッサ-仮想化エンジン
　Intel VT-x/EPT または AMD-V/RVIを仮想化
```

---

## 仮想環境構成ファイル
```
$ vi Vagrantfile 
$ cat Vagrantfile 
#!/usr/bin/ruby

Vagrant.configure('2') do |config|
  config.vm.box = "ubuntu/xenial64"

  {
    'ubuntu01' => '10.10.10.1',
    'ubuntu02' => '10.10.10.2'
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
$ vi ansible.cfg 
$ cat ansible.cfg 
[defaults]
inventory = /home/user/git/ansible-practice/hosts
remote_user = vagrant
host_key_checking = False
roles_path = ~/git/ansible-practice/roles/
private_key_file = ~/git/.vagrant/machines/{{ inventory_hostname }}/virtualbox/private_key
roles_path = ./roles

[privilege_escalation]
become = True
$ 
```
## 環境構築
```
$ vagrant status
$ vagrant up
$ vagrant status
$ vagrant ssh ubuntu01
```

---
## その他
```
仮想環境を構築中によくロック画面に言ったため、以下コマンドでロック画面にいかないようにする。
$ sudo -u 'username' -H dbus-launch gsettings set org.gnome.desktop.screensaver lock-enabled false
$ sudo -u 'username' -H dbus-launch gsettings set org.gnome.desktop.screensaver ubuntu-lock-on-suspend false
```

---
