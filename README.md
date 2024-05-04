
# ssh
* ssh ed25519 key gen sample
```{#lst:id python caption="ssh"}
ssh-keygen -t ed25519 -C "miya@example.bb.aa" -f ~/id_ed25519_miya
ssh -T git@github.com -i ~/id_ed25519_miya 
export GIT_SSH_COMMAND="ssh -i ~/id_ed25519_miya  -F /dev/null"
```
> note
> ssh key はgithub上にも登録すること

# git
* git command sample
```
git clone git@github.com:twomidle/ansible.git  
修正作業  
git add *  
git rm `git ls-files --deleted` 
git status  
git commit -m "YYYY/MM/DD commit"  
git status  
git push -u origin  
```

## ansible
# ansible install
python3 -m pip install --user ansible

# ansible install atode kakunin
pip3 install amazon.aws  
pip3 install boto3

# ansible playbook sample
```{#lst:id python caption="ssh"}
cat << 'EOF' > sample/secret_messages.yml 
# Title: secret_messages.yml 

---
- hosts: localhost
  collections:
    - netapp.ontap
    - amazon.aws
  gather_facts: false
  name: test Playbook to create a cluster peering

  tasks:
    - name: Create cluster peer
      debug: msg="{{ lookup('amazon.aws.aws_secret', 'FSx.password', nested=true) }}"
EOF
```

ansible-playbook sample/secret_messages.yml 
