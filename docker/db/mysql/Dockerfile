FROM mysql:8.0.14

#MySQL設定ファイルをイメージ内にコピー
ADD ./resources/conf.d/my.cnf /etc/mysql/conf.d/my.cnf
ADD ./resources/conf.d/default_authentication.cnf /etc/mysql/conf.d/default_authentication.cnf
RUN chmod -R 0644 /etc/mysql/conf.d/my.cnf
RUN chmod -R 0444 /etc/mysql/conf.d/default_authentication.cnf