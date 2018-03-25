#Ansibleでいつも同じ環境に

---

## 環境構築だるいですよね... :weary:

---

## 何がだるいか

- そもそもインストールがだるい
- 何をインストールしたか覚えていない

---

# どうしたらよいか...

---

# :bulb: Ansibleあるやん...

---

## Ansibleとは

- 環境構築の自動化ツール
- https://www.ansible.com

---

## 何ができるのか

- 色々な環境にワンライナーで環境構築できる
- 構成をコード化できる
- 難度実行しても同じ結果になる

---

## とりあえず、かいてみよう

### 最低限必要なもの

- inventry
  - どこに環境構築するかを定義する
- playbook
  - なにをインストールするか、どんな処理を実行するか定義する

---

## inventry

ini形式でかける、実行対象がローカルだけなら下記を用意すればOK

```
localhost
```

頑張れば、こんな感じにも書けます

```
[web]
web01.example.com
web02.example.com

[web:vars]
ansible_ssh_port=20022

[db]
db01.example.com
```

http://www.slideshare.net/kk_Ataka/ansibleinfrastructure-as-code　より抜粋

---

## playbook

yamlで書ける。
3つの役割に分かれています

- target
  - どこにだれがインストールするのか 
- vars
  - tasksで使う変数を定義 
- tasks
  - 実際のインストール処理を定義

---

## target

```
---
- name: Setup macbook
  hosts: localhost
  connection: local
  gather_facts: no
  roles: 
    - { role: homebrew, tags: [ homebrew ] }
    - { role: homebrew-cask, tags: [ homebrew-cask ] }
    - { role: prezto, tags: [ prezto ]}
```

---

## vars

```
homebrew_taps:
- homebrew/cask

homebrew_packages:
  - { name: elixir }
  - { name: erlang }
  - { name: git }
  - { name: go, install_options: cross-compile-all }
  - { name: openssl, state: linked, install_options: force }
  - { name: scala }
  - { name: tig }
  - { name: zsh, install_options: disable-etcdir }
```

---

## tasks

```

- name: Add tap repositories of homebrew
  homebrew_tap: tap={{ item }} state=present
  with_items: "{{homebrew_taps}}"

- name: Update homebrew
  homebrew: update_homebrew=yes

- name: Install brew packages
  homebrew: >
    name={{ item.name }}
    state={{ item.state | default('latest' )}}
    install_options={{
      item.Install | default() | join(',')
    }}
  with_items: "{{homebrew_packages}}"
  register: brew_result
```

---

## ぜんぶフルフルで作ると...

こんな感じ

```
/
`- roles
|  `- some_task
|     `- tasks
|     |  `- main.yaml
|     `- vars
|        `- main.yaml
`- hosts
`- localhost.yaml
`- site.yaml
```

ディレクトリ構成の詳細は

http://docs.ansible.com/ansible/playbooks_best_practices.html

を参照してください

---

## Ansibleでよかったなーと思うこと

- 書くの楽
- DRY RUNができる
- 自分の構成を可視化できる

---

## Ansibleで不満なところ

- Pythonをクライアントにもホストにもいれないといけない
  - 最近は普通に入っていたりするので気にしなくても良いかも...

---

## FAQ

- Windowsってどうなん?
  - :innocent:... ChocolateyとかPackageManagementなどあるけど現状微妙かも
    - Homebrewほど洗練されていない印象

- 仕事には活用できるの?
  - 開発環境をワンライナー構築させるのには良いかも

---

# みんなAnsible使おうぜ :raising_hand: