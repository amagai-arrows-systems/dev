# Dockerfile
FROM docker.elastic.co/elasticsearch/elasticsearch:8.14.3

# 必要なツールのインストール
USER root
RUN apt-get update && apt-get install -y wget unzip ca-certificates

# Kuromojiプラグインのインストール
RUN /usr/share/elasticsearch/bin/elasticsearch-plugin install analysis-kuromoji

# analysis-icuプラグインのインストール
RUN /usr/share/elasticsearch/bin/elasticsearch-plugin install analysis-icu

# ltr-pluginのダウンロードとインストール
RUN wget -O /tmp/ltr-plugin-v1.5.9-es8.14.3.zip https://github.com/o19s/elasticsearch-learning-to-rank/releases/download/v1.5.9-es8.14.3/ltr-plugin-v1.5.9-es8.14.3.zip && \
    /usr/share/elasticsearch/bin/elasticsearch-plugin install --batch file:///tmp/ltr-plugin-v1.5.9-es8.14.3.zip && \
    rm /tmp/ltr-plugin-v1.5.9-es8.14.3.zip

# Sudachiプラグインのダウンロードとインストール
RUN /usr/share/elasticsearch/bin/elasticsearch-plugin install https://github.com/WorksApplications/elasticsearch-sudachi/releases/download/v3.3.0/elasticsearch-8.14.3-analysis-sudachi-3.3.0.zip

# Sudachi辞書ファイルのダウンロードと配置
RUN wget https://d2ej7fkh96fzlu.cloudfront.net/sudachidict/sudachi-dictionary-latest-core.zip && \
    unzip sudachi-dictionary-latest-core.zip && \
    mkdir config/sudachi && \
    cp sudachi-dictionary-*/system_core.dic config/sudachi/system_core.dic

# ユーザー辞書の配置
RUN mkdir -p /usr/share/elasticsearch/config/analysis
# COPY user_dict.csv /usr/share/elasticsearch/config/user_dict.csv
# COPY user_dict.csv /usr/share/elasticsearch/config/analysis/test-user_dict.csv
# COPY resources/config/user_test.dic /usr/share/elasticsearch/config/sudachi/user_test.dic
# COPY resources/config/sudachi.json /usr/share/elasticsearch/config/sudachi/sudachi.json
RUN touch /usr/share/elasticsearch/config/dummy_user_dict.csv

# 同義語辞書の登録
RUN touch /usr/share/elasticsearch/config/analysis/test-synonym.txt
# COPY resources/config/test-synonym.txt /usr/share/elasticsearch/config/analysis/test-synonym.txt

# Javaセキュリティポリシーファイルのコピー
COPY resources/config/java.policy /etc/elasticsearch/java.policy

# パーミッションの設定
# RUN chmod 644 /usr/share/elasticsearch/config/sudachi/user_test.dic
RUN chmod 644 /usr/share/elasticsearch/config/sudachi/system_core.dic
# RUN chmod 644 /usr/share/elasticsearch/config/sudachi/sudachi.json
RUN chmod 644 /etc/elasticsearch/java.policy

USER elasticsearch