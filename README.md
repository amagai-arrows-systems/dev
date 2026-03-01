# WSL開発環境について

## ディレクトリ構成

<pre>
.
├── README.md
├── compose.yml
├── docker
│   ├── portainer_data
│   └── serena_mcp
└── pdev
    ├── README.md
    └── _pdev_onboarding.md
</pre>

### docker ディレクトリ

- 各種サービス毎にディレクトリを作成する

### pdev

- 個人開発するディレクトリ

## トップレベルのcompose.yml

- Portainer を使用した Docker での仮想環境を確立するための定義している
- 各種Docker環境を呼び出すための定義を集約している
- 複数のネットワーク構成の定義をしている
- 必要があれば、Voluemes設定もこのファイルで行う
