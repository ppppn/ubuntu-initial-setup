# Ubuntuインストール時構成用 Ansible Playbook

## はじめに
Ubuntuをクリーンインストールすると、毎回、付け足し付け足しの秘伝のシェルスクリプトを回していたので、勉強がてらAnsibleでやってみることにしました。
勉強中だから、いい方法があったらどんどん変えていく所存です。

## rolesの説明
### set_repositories
- Ubuntu Japanese TeamのWebサイトの[Ubuntuの日本語環境](http://ubuntulinux.jp/japanese) の「方法2」を自動でおこないます
    - 鍵の追加
    - sources.listの追加
- APTのミラーサーバーを[日本国内のサーバー](https://www.ubuntulinux.jp/ubuntu/mirrors) に変更します
    - vars/main.yml に列挙してあるうち、お好みのサーバーを1つ選んで、他はすべてコメントアウトしてください
    - ただし、 partner レポジトリは canonical.com にしかないようなので、ここは置換しないようにしています
- 標準外レポジトリを追加します
    - ppaとか
- apt upgradeをおこないます

### install_essentials
- サーバー、デスクトップを問わず、必ずおこなうソフトウェアのインストールと削除をおこないます
    - インストールするソフトウェアは vars/main.yml に記載します
        - install_packages にリスト形式でaptレポジトリからインストールするパッケージ名を列挙します
        - install_deb_packages にリスト形式でインストールする非レポジトリパッケージアーカイブへのパスを列挙します
            - URLでもOK
        - remove_packages にリスト形式で削除するパッケージ名を列挙します

### install_desktop_essentials
- デスクトップ環境で必ずおこなうソフトウェアのインストールと削除をおこないます
    - インストールするソフトウェアは vars/main.yml に記載します
        - install_packages にリスト形式でaptレポジトリからインストールするパッケージ名を列挙します
        - install_deb_packages にリスト形式でインストールする非レポジトリパッケージアーカイブへのパスを列挙します
            - URLでもOK
        - remove_packages にリスト形式で削除するパッケージ名を列挙します

### update_alternatives
- update-alternatives コマンドの実行に相当します
    - update-alternatives --config <name> <path> に対応するように、 vars/main.yml に記載します
    - 私は editor をいつも vim に変更しているので、そのように書いています

### docker_ce
- [Docker CE](https://docs.docker.com/engine/installation/linux/ubuntu/#install-using-the-repository) の手順にしたがって、docker-ceパッケージをインストールします
  - 前提パッケージのインストール
  - 鍵の追加
  - sources.listの追加
  - `docker-ce` のインストール
- 2017/11/20現在、17.10用のレポジトリがないため、無効化

## 流し方
- localhost.yml は、localhostに対し、上記すべてのロールを適用するプレイブックです
- 次のコマンドを叩けば、プレイブックが流れます:
   ``` ansible-playbook -i hosts localhost.yml -K ```
    - sudo のパスワードが要求されます

- 本番環境でおこなう前に、仮想環境等で実験をおこなってから実用してください
    - 本プレイブックにより損害が生じても、作成者は責任を負いません
