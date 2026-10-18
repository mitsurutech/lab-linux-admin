# Linux 管理のラボ：1台の Ubuntu VM でユーザー、権限、cron、systemd を学ぶ

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml)

授業でそのまま使える **Linux 管理のラボ**です。学校やカレッジが生徒一人ひとりに専用の Ubuntu マシンとブラウザー上のターミナルを渡せる [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)で提供しています。生徒は小さなサーバーの管理者になり、入社1年目のシステム管理者が行う4つの仕事をします。**ユーザーとグループの追加**、**共有フォルダーの権限設定**、**cron によるジョブのスケジュール**、**systemd サービスの作成と有効化**です。生徒のコンピューターには何もインストールせず、ラボはスクリプトが採点するので、教員が1台ずつマシンを読む必要はありません。

## 4つの課題

| 課題 | 生徒がすること |
| --- | --- |
| 1. ユーザーとグループ | 通常のグループ `devs` と、ホームフォルダーと bash を持つ通常のユーザー `maria` を作る。ユーザーはそのグループに入れ、管理者にはしない |
| 2. 共有フォルダー | `/srv/projects` をチームのフォルダーにする。グループのメンバーは読み取り、書き込み、フォルダーへの移動ができ、中で作ったファイルは自動的にグループのものになり、それ以外の人は一切アクセスできない |
| 3. スケジュールされたジョブ | ラボの `backup.sh` を、`/etc/cron.d` のファイルから毎日 02:30 に root で実行する。cron が必要とする書き方にする |
| 4. サービス | ラボの `heartbeat.sh` 用の systemd ユニットファイルを書き、失敗したら再起動されるようにし、起動のたびに開始されるよう有効にする |

ラボは生徒のホームフォルダーに、何をするかを書いた README を置きますが、答えは一つも書いてありません。コマンドも、モードも、ユニットファイルもありません。意図的に小さくしてあるので、**ユーザーと権限、cron、systemd** の最初のレッスンにちょうどよく、ファイルをコピーしてディスク、LVM、ファイアウォールなどを足すこともできます。

## 採点のしくみ

`check` スクリプトは、新しく起動した後の保存済みマシンのコピーに対して root で実行されます。そのため、見えるのは**保存された**ものだけで、入力したものの再起動で失われたコマンドは見えません。4つの課題を順に調べ、最初に間違っているところで止まります。最初の1行にどの課題がなぜ間違っているかが出ます。たとえば `NOT PASSED: task 3: the user field is /usr/local/bin/backup.sh, it must be root` です。実際の状態を読みます。アカウントデータベースのユーザーとグループ、フォルダーの所有者とモード、新しいユーザーとグループ外の人が実際に作ったファイル、cron ファイルの所有者、権限、末尾、ジョブの1行、ユニットファイルの `ExecStart`、`Restart`、`WantedBy`、そして `systemctl enable` が作るリンクです。ユーザーの crontab、`chmod 777` で全開にしたフォルダー、開始しただけで有効にしていないサービス、リンクの代わりに置いたユニットファイルのコピーは、すべて不合格になります。

## ファイルの中身

`linux-admin.lab.yaml` は、プラットフォームが実行するそのままの **VM ラボの定義**一式です。

| キー | 内容 |
| --- | --- |
| `cpus`, `memory`, `disk` | 生徒ごとの VM の大きさ：CPU 1個、1024 MB、10 GB |
| `open` | ブラウザーで開くポート：なし。ラボはターミナルです |
| `setup` | 各生徒の VM が初めて起動したときに、root で一度だけ実行されるスクリプト。Ubuntu のアーカイブから `cron` をインストールし、2つの小さなスクリプトと共有フォルダーを作り、ラボの README を書きます |
| `check` | 上で説明した採点スクリプト。終了ステータスが結果で、`0` なら合格です |

## プラットフォームで使う

**クラスの課題として出すには：**

1. モジュールで課題を追加し、**Ubuntu VM ラボ**のカードを選びます。
2. サンプルのボタンを押すか、`linux-admin.lab.yaml` の内容を貼り付けます。サンプルを使うとテキストが置き換わり、元に戻すと自分のテキストが戻ります。
3. 公開して、まず自分用のコピーで試します。生徒にはそれぞれ専用の VM が渡され、セッションをまたいで保たれます。
4. 他の課題と同じように配布します。提出されたら、クラス一覧で生徒の行からチェックを実行します。保存された生徒のマシンのコピーを起動し（保存し直しはされません）、合格したかどうかと出力された内容を表示します。

残りはヘルプマニュアルの[このラボのページ](https://mitsurutech.co.uk/ja/help/lab-linux-admin)、[Ubuntu VM ラボのガイド](https://mitsurutech.co.uk/ja/help/labs)、[サンプルラボのページ](https://mitsurutech.co.uk/ja/help/sample-labs)をご覧ください。まだカレッジのアカウントがありませんか？ 生徒50人までなら[最初の1年は無料](https://mitsurutech.co.uk/ja/pricing)で、[登録](https://mitsurutech.co.uk/ja/signup)にはメールアドレスだけが必要です。

## ファイルの検査

push のたびに、`.github/workflows/check.yml` が次のことを調べます。ファイルが上のキーを持つ有効な YAML であること、`setup` と `check` がそれぞれプラットフォームの 16 KB の上限に収まっていること。VM は起動しません。それはプラットフォーム自身のマシンで行います。

## ラボでできないこと

VM が接続できるのは Ubuntu のパッケージアーカイブ、PyPI、npm、GitHub だけで、それ以外には接続できません。そのため、ラボからインターネット上のホストを指すことはできません。VM は1台なので、ネットワーク越しに管理する2台目のマシンはありません。

## 提供元

[Mitsuru Technologies](https://mitsurutech.co.uk/ja) が [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のために作りました。学校とカレッジ向けに、Python、Java、Go、Node、SQL、Linux をブラウザーで学べ、コンテナラボ、VM ラボ、GitHub からデプロイする Web アプリ、自動採点されるクイズがそろっています。アカウントなしで、[プレイグラウンド](https://mitsurutech.co.uk/ja/playground)でコードを試せます。

同じところのほかのラボ：[lab-netlab](https://github.com/mitsurutech/lab-netlab)、[lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop)、[lab-dvwa](https://github.com/mitsurutech/lab-dvwa)、[lab-webgoat](https://github.com/mitsurutech/lab-webgoat)、[lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin)、[lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea)、[lab-sample](https://github.com/mitsurutech/lab-sample)。

## ライセンス

この作品は [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) でライセンスされています。Mitsuru Technologies Ltd のクレジットを表示し、商用に使わず、これをもとに作ったものを同じライセンスで共有するなら、学習と授業のために共有・改変できます。全文は `LICENSE` ファイルにあります。

`cron` は VM の起動時に Ubuntu のアーカイブからインストールされます。`cron` 自体はここでは配布していません。
