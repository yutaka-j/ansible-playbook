# ansible-playbook
ansibleでいろいろ遊ぶためのplaybookを貯める場所．

普段はサーバ管理が割とメインな割に，今まで自動化して来なかったので，勉強も兼ねている．

## 前提
練習用なので，基本的にはvagrantで動かすことを想定している．

おそらく必要なのは以下のもの
- VirtualBox 4.2.x
- vagrant 1.2
- ansible 1.2

細かいインストール方法とかに関してはブロクに書いた．
- [VirtualBox導入](http://yutaka-j.hatenablog.com/entry/2013/06/14/100511)
- [vagrant導入](http://yutaka-j.hatenablog.com/entry/2013/06/14/141749)
- (近いうちにansible導入も書く)

## 使い方
このリポジトリをクローンしてVMを起動．

ちなみに，boxはprecise32なので，持ってない場合はダウンロードが始まります．
```
git clone https://github.com/yutaka-j/ansible-playbook.git
cd ansible-playbook
vagrant up
```

playbooksへ移動して，`ansible-playbook`する．

-Dを追加して実行すると，実際に裏で何をやってるか見れる．デバッグとかにどうぞ．
```
cd playbooks
ansible-playbook main.yml -i hosts/local -k
# ansible-playbook main.yml -i hosts/local -k -D
```

SSHのパスワードを聞いてくるので「vagrant」と入力したら，ansibleが走る．
```
SSH password:
```

## 実行結果
多分こんなかんじになる．

失敗した場合は適当に直してね．

(roles使ってるので，ansible1.2じゃないと動かない)

```
PLAY [local] ****************************************************************** 

GATHERING FACTS *************************************************************** 
ok: [192.168.33.12]

TASK: [Install editor] ******************************************************** 
changed: [192.168.33.12] => (item=emacs)

TASK: [Install basic pkgs] **************************************************** 
changed: [192.168.33.12] => (item=zip,curl,tmux,zsh)

TASK: [Install basic devel pkgs] ********************************************** 
changed: [192.168.33.12] => (item=build-essential,git,python-dev,python-setuptools,python-pip)

TASK: [Write tmux config file] ************************************************ 
changed: [192.168.33.12]

TASK: [Write zsh config file] ************************************************* 
changed: [192.168.33.12]

TASK: [Pip install basic python pkgs] ***************************************** 
changed: [192.168.33.12] => (item=ipython)
changed: [192.168.33.12] => (item=Flask)
changed: [192.168.33.12] => (item=pep8)
changed: [192.168.33.12] => (item=parse)
changed: [192.168.33.12] => (item=PIL)
changed: [192.168.33.12] => (item=msgpack-python)
changed: [192.168.33.12] => (item=msgpack-rpc-python)
changed: [192.168.33.12] => (item=requests)

TASK: [Git clone my repos] **************************************************** 
changed: [192.168.33.12] => (item={'repo': 'https://github.com/yutaka-j/ansible-playbook.git', 'dest': '~/repos/ansible-playbook'})

PLAY RECAP ******************************************************************** 
192.168.33.12              : ok=8    changed=7    unreachable=0    failed=0   
```

冪等性とやらで，2回目の実行時は，環境に変化がなければ何もしない．(changed=0になる)

(つまり`f(f(x)) == f(x)`という事らしい．賢い．)

```
PLAY [local] ****************************************************************** 

GATHERING FACTS *************************************************************** 
ok: [192.168.33.12]

TASK: [Install editor] ******************************************************** 
ok: [192.168.33.12] => (item=emacs)

TASK: [Install basic pkgs] **************************************************** 
ok: [192.168.33.12] => (item=zip,curl,tmux,zsh)

TASK: [Install basic devel pkgs] ********************************************** 
ok: [192.168.33.12] => (item=build-essential,git,python-dev,python-setuptools,python-pip)

TASK: [Write tmux config file] ************************************************ 
ok: [192.168.33.12]

TASK: [Write zsh config file] ************************************************* 
ok: [192.168.33.12]

TASK: [Pip install basic python pkgs] ***************************************** 
ok: [192.168.33.12] => (item=ipython)
ok: [192.168.33.12] => (item=Flask)
ok: [192.168.33.12] => (item=pep8)
ok: [192.168.33.12] => (item=parse)
ok: [192.168.33.12] => (item=PIL)
ok: [192.168.33.12] => (item=msgpack-python)
ok: [192.168.33.12] => (item=msgpack-rpc-python)
ok: [192.168.33.12] => (item=requests)

TASK: [Git clone my repos] **************************************************** 
ok: [192.168.33.12] => (item={'repo': 'https://github.com/yutaka-j/ansible-playbook.git', 'dest': '~/repos/ansible-playbook'})

PLAY RECAP ******************************************************************** 
192.168.33.12              : ok=8    changed=0    unreachable=0    failed=0   
```

## TODO？
一部のplaybookでコメントアウトしてある部分がある．

それをちゃんと動かせるようにしたいな･･･

具体的には以下の2つ．知ってる人がいたら教えてください．
- ansibleでデフォルトのshellを変更(chsh)  
  chshがパスワード聞いてくるのでうまく行かない様子．
- templateモジュールでvarsの展開  
  なぜか展開されなかった･･･

## エラー
こんなエラーが出た場合，パスワードが間違っているので，再度`ansible-playbook`をやり直す．

```
PLAY [local] ****************************************************************** 

GATHERING FACTS *************************************************************** 
fatal: [192.168.33.12] => {'msg': 'FAILED: Authentication failed.', 'failed': True}

TASK: [Install editor] ******************************************************** 
FATAL: no hosts matched or all hosts have already failed -- aborting


PLAY RECAP ******************************************************************** 
           to retry, use: --limit @/var/tmp/ansible/main.retry

192.168.33.12              : ok=0    changed=0    unreachable=1    failed=0   
```