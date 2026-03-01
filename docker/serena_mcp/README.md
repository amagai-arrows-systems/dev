# Serena MCP (Docker) — 設定解析と注意点

このディレクトリは Serena MCP サーバを Docker で起動するための構成を含みます。
以下は現状の設定解析、過去に行った修正内容、及び今後新規で設定する際に躓かないためのチェックリストと推奨テンプレートです。

## 現状サマリ
- 起動コマンド: `serena start-mcp-server` を使って MCP サーバを起動する必要があります。以前は `serena` のみで CLI ヘルプが出力され、コンテナが再起動するループになっていました。
- 設定ファイル（マウント先）: `./.serena_mcp_config/serena_config.yml` をコンテナ内の `/workspaces/serena/config/serena_config.yml` にマウントしています。
- ダッシュボード: Web ダッシュボードはデフォルトで `web_dashboard_listen_address` にバインドされ、`web_dashboard_port`（デフォルト 24282）で公開されます。コンテナ外からアクセスするなら `0.0.0.0` でバインドする必要があります。

## 起きた問題と対処
1. コンテナが再起動する（ERR_CONNECTION_REFUSED）
	 - 原因: `compose.yml` の `command` が `serena` のみで、CLI ヘルプを表示して終了していたため。
	 - 対処: `command: serena start-mcp-server` に修正。

2. `serena_config.yml` の読み込みエラー（`projects` key not found / 型エラー）
	 - 原因: `projects` が期待される形式（リストのパス文字列）ではなかった、または誤った YAML 構造により ruamel.yaml の `CommentedMap` が渡され Path() に直接入ってしまった。
	 - 対処: 最小限の `projects` エントリを追加して動作確認を行い、その後設定を正しい形式に整備。最終的に `projects: []` もしくは `projects: ["/workspaces/pdev"]` のような文字列のリストが正しい。

3. ダッシュボードがホストから見えない
	 - 原因: `web_dashboard_listen_address` が `127.0.0.1` のままだとコンテナ内ループバックにのみバインドされ、ホストの公開ポート経由で接続できない場合がある。
	 - 対処: `web_dashboard_listen_address: 0.0.0.0` に変更してコンテナ外から到達可能にした。

## 推奨する `serena_config.yml` の最小テンプレート
以下を新しい設定作成時のベースにしてください（必要に応じてコメントや追加設定を加える）。

```yaml
# 保存先等
storage_path: "/workspaces/serena/config/storage"
log_path: "/workspaces/serena/config/logs"

allowed_directories:
	- "/workspaces/pdev"

web_dashboard: true
web_dashboard_port: 24282
web_dashboard_listen_address: 0.0.0.0

# 登録プロジェクトはパスのリスト（空でも可）
projects:
	- "/workspaces/pdev"

excluded_tools: []
included_optional_tools: []
fixed_tools: []

default_modes:
	- interactive
	- editing

language_backend: LSP
```

## 設定作成時のチェックリスト（手順）
1. `serena_config.yml` を上記テンプレートで作成する（YAML のインデントと型に注意）。
2. `projects` は必ず文字列パスのリストにする（辞書形式や ruamel の CommentedMap を直接渡さない）。
3. `compose.yml` の `command` を `serena start-mcp-server` にする。
4. ダッシュボードをホストから見たい場合は `web_dashboard_listen_address: 0.0.0.0` を設定し、`ports:` で公開ポート（例 `24282:24282`）があることを確認する。
5. コンテナ起動後、ログを確認して fatal exception が出ていないかチェックする：

```sh
docker compose -f docker/serena_mcp/compose.yml up -d
docker logs --tail 200 serena_mcp
curl -4 -v http://127.0.0.1:24282/dashboard/
```

## トラブルシュートのヒント
- YAML の型エラー（TypeError: expected str, bytes or os.PathLike object, not CommentedMap）が出た場合、`projects` の中身が期待の文字列リストであるかを疑う。
- 設定を編集後はコンテナを再作成（`--force-recreate`）して確実に反映させる。
- ポートが LISTEN しているかはホストで `ss -ltnp | grep 24282` を実行して確認する。

---

必要ならこの README を拡張して「よくあるエラー（ログ抜粋）と解決法」セクションを追加します。追加を希望しますか？

