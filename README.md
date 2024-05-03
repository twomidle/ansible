
## git
# ssh ed25519 key gen
ssh-keygen -t ed25519 -C "miya@example.bb.aa" -f ~/id_ed25519_miya
# githubにも登録
ssh -T git@github.com -i ~/id_ed25519_miya 
export GIT_SSH_COMMAND="ssh -i ~/id_ed25519_miya  -F /dev/null"
git clone git@github.com:twomidle/ansible.git
git add *
git rm `git ls-files --deleted`
git status
git commit -m "YYYY/MM/DD commit"
git status
git push -u origin

## ansible
# ansible install
python3 -m pip install --user ansible

# ansible install atode kakunin
pip3 install amazon.aws
pip3 install boto3

# ansible playbook sample
ansible-playbook sample/secret_messages.yml 





