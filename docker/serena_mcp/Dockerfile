FROM ghcr.io/oraios/serena:latest

USER root

# Goをインストール
RUN apt-get update && apt-get install -y golang-go && \
    # gopls をバイナリとしてインストール
    go install golang.org/x/tools/gopls@latest

# Goのバージョンを環境変数に設定
ENV GO_VERSION=1.24.4
ENV GO_PATH=/root/go
ENV PATH="${GO_PATH}/bin:${PATH}"

# 2. .Netをインストール

# 1. 必要なツールをインストール
RUN apt-get update && apt-get install -y wget gpg ca-certificates && rm -rf /var/lib/apt/lists/*

# 2. 公式の Debian 13 用リポジトリを登録（URLを最後まで正確に！）
RUN wget https://packages.microsoft.com/config/debian/13/packages-microsoft-prod.deb -O packages-microsoft-prod.deb && \
    dpkg -i packages-microsoft-prod.deb && \
    rm packages-microsoft-prod.deb

# 3. リポジトリを更新して .NET 10.0 SDK をインストール
RUN apt-get update && \
    apt-get install -y dotnet-sdk-10.0

# 3. 環境変数の設定
ENV DOTNET_ROOT=/usr/bin/dotnet
ENV PATH="${DOTNET_ROOT}:${PATH}"
# Serenaが内部でチェックする変数も固定
ENV DOTNET_VERSION=10.0

# 4. 念のためビルド中に確認
RUN dotnet --version