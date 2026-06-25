# ==========================================
# 1. Webサーバ (Apache) の構築とベーシック認証設定
# ==========================================

# パッケージのインストールと自動起動設定
sudo apt update && sudo apt install apache2 -y && sudo systemctl enable --now apache2 || sudo dnf install httpd -y && sudo systemctl enable --now httpd

# ベーシック認証用ユーザー（例：webuser）とパスワードファイルの作成
# ※実行後にパスワード入力を2回求められます。
sudo htpasswd -c /etc/apache2/.htpasswd webuser || sudo htpasswd -c /etc/httpd/.htpasswd webuser

# ------------------------------------------
# 設定ファイルの編集（手動で追記してください）
# ------------------------------------------
# 【Ubuntu/Debian系の場合】
# 設定ファイル: /etc/apache2/sites-available/000-default.conf
# 編集内容: <VirtualHost *:80> ブロックの中に以下を追記します。
# <Directory "/var/www/html">
#     AuthType Basic
#     AuthName "Restricted Area"
#     AuthUserFile /etc/apache2/.htpasswd
#     Require valid-user
# </Directory>
# ------------------------------------------
# 【RHEL/AlmaLinux系の場合】
# 設定ファイル: /etc/httpd/conf/httpd.conf
# 編集内容: 既存の <Directory "/var/www/html"> ブロックの中に以下を追記します。
#     AuthType Basic
#     AuthName "Restricted Area"
#     AuthUserFile /etc/httpd/.htpasswd
#     Require valid-user
# ------------------------------------------

# Webサーバの再起動（設定の反映）
sudo systemctl restart apache2 || sudo systemctl restart httpd

# ファイアウォールの開放（Web用：80番ポート）
sudo ufw allow 80/tcp && sudo ufw reload || sudo firewall-cmd --permanent --add-service=http && sudo firewall-cmd --reload


# ==========================================
# 2. FTPサーバ (vsftpd) の構築
# ==========================================

# パッケージのインストールと自動起動設定
sudo apt update && sudo apt install vsftpd -y && sudo systemctl enable --now vsftpd || sudo dnf install vsftpd -y && sudo systemctl enable --now vsftpd

# ------------------------------------------
# 設定ファイルの編集（手動で書き換え・追記してください）
# ------------------------------------------
# 設定ファイル: /etc/vsftpd.conf (Ubuntu) または /etc/vsftpd/vsftpd.conf (RHEL系)
# 編集内容: 以下の項目を探して値を変更し、最下部に allow_writeable_chroot を追記します。
# anonymous_enable=NO
# local_enable=YES
# write_enable=YES
# chroot_local_user=YES
# allow_writeable_chroot=YES
# ------------------------------------------

# FTPサーバの再起動（設定の反映）
sudo systemctl restart vsftpd

# ファイアウォールの開放（FTP用：21番ポート）
sudo ufw allow 21/tcp && sudo ufw reload || sudo firewall-cmd --permanent --add-service=ftp && sudo firewall-cmd --reload


# ==========================================
# 3. Telnetサーバ の構築
# ==========================================

# パッケージのインストールと自動起動設定
sudo apt update && sudo apt install telnetd -y && sudo systemctl enable --now openbsd-inetd || sudo dnf install telnet-server -y && sudo systemctl enable --now telnet.socket

# ------------------------------------------
# 動作確認方法（手動でテスト用）
# ------------------------------------------
# root以外の一般ユーザーを作成、または用意した上で以下を実行します。
# telnet localhost
# ------------------------------------------

# ファイアウォールの開放（Telnet用：23番ポート）
sudo ufw allow 23/tcp && sudo ufw reload || sudo firewall-cmd --permanent --add-service=telnet && sudo firewall-cmd --reload
