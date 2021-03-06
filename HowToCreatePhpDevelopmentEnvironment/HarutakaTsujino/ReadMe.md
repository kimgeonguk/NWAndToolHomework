# nginxとPHP_FPMをUbuntuで

## 前書き
一度環境構築を行いましたが、完全に理解できたわけではないので、
それを調べまとめていきたいと思います。

## nginxとPHP_FPMって？
今回の環境構築の二大要素であるnginxとPHP_FPM解説していきたいと思います。

### nginx
- 読み方はエンジンエックス(エヌジンエックスではない)
- Webサーバー
- 無料でオープンソース
- シングルスレッドモデルのイベント駆動型
- PHPを**自身**で動かせない

#### Webサーバーって？？
HTTPリクエストを送った時に、何かしら反応を返してくれるプログラム

#### シングルスレッドモデルのイベント駆動型って？？
現在の接続状態によってプロセスを呼び出す。
待ち時間がほぼないので同時接続に強くメモリをあまり使わない

#### よく使われるApache
- 正式名称はApache HTTP Server
- Webサーバー
- 同じく無料でオープンソース
- PHPを自身で動かせる**やつもある**
- マルチプロセスのプロセス起動型

##### マルチプロセスのプロセス起動型って？？
接続ごとに親プロセスをコピーした子プロセスで処理を行う。
コピーにコストがかかりそれぞれのプロセスごとに独自のメモリ領域を使うので同時接続に弱い

###### なんでコピーする必要があるの そもそもプロセスとは？？？
今回のテーマとずれるので説明はしないが[このとても分かりやすいスライド](https://openstandia.jp/pdf/140228_osc_seminar_ssof8.pdf)を読んだらわかる。

### PHP_FPM
- Fast CGI Process Manager
- APサーバー

PHPを実行してくれる

#### APサーバーって？？
Webサーバから受け取ったリクエストをもとに、Javaやphp、Rubyなどを実行し、Webサーバに結果を返すプログラム

#### CGIって？？
- Common Gateway Interface（コモン・ゲートウェイ・インタフェース)
- WebサーバーとAPサーバーとの連携のためのプロトコル
- 要求がある度にプロセスの生成と破棄を行う

##### Fast CGIって？？？
- CGIの強化版
- プロセスの生成と破棄を毎回行わない

## nginxとPHP_FPMの通信について
TCP or UNIXドメインソケットがメジャーらしい

### 通信の種類がわからない
そもそも通信には[層](https://ja.wikibooks.org/wiki/TCP/IP%E5%85%A5%E9%96%80_%E5%90%84%E5%B1%A4%E3%81%AE%E5%BD%B9%E5%89%B2)がある
- TCPはトランスポート層でデータが届いたかのチェックをしたり到着順序などに厳しい信頼性が高い通信法
- UNIXドメインソケットはTCPより上の層で一つのマシン内で行う通信、ファイルパスで通信を行う

## 環境構築
ほとんど解説し終えたのでざっくりと説明していきます。
1. PHP_FPMをインストール
2. 動作モードを確認するため/etc/php/7.2/fpm/php-fpm.confをチェック
3. 2のファイルで設定は外部のファイルであることが分かるのでそのファイル/etc/php/7.2/fpm/pool.d/www.confをチェック
4. listenの項目を確認すると/run/php/php7.2-fpm.sockでソケット通信をするようにされていることがわかる
5. OS起動時にPHP_FPMが起動するようになっているかチェック
6. nginxのインストール
7. /etc/nginx/sites-enabled/defaultで拡張子.phpのファイルが来たら4で確認したファイルパスでソケット通信を行うように設定
8. OS起動時にnginxが起動するようになっているかチェック

## 後書き
理解したらめちゃくちゃ簡単なのはいつものこと
