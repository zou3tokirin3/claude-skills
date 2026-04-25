---
name: zunda-agent
description: Guides setup and behavior of zunda-agent, the Claude Code Stop hook that summarizes the last assistant message in ずんだもん style and reads it aloud via VOICEVOX or macOS say. Use when the user mentions zunda-agent, Stop hook, ずんだもん, VOICEVOX, task-completion TTS, or configuring hooks for Claude Code.
---

# zunda-agent

## 概要

Claude Code がタスクを完了するたびに **Stop** フックが走り、(1) 最終アシスタント文を取得 (2) Claude CLI（`claude -p`）またはローカル処理で要約 (3) 端末に要約を表示（`uv` があれば rich パネル）(4) macOS では通知センターにも表示可能 (5) VOICEVOX（ずんだもん）優先、だめなら `say` で読み上げ、という流れになる。会話停止はブロックしない（フックは常に成功で終了）。

リポジトリのエントリは `.claude/hooks/stop-mvp-pipeline.sh`。`REPO_ROOT` はフックから `scripts/` を参照するため、配置したパスと一致させる。

## 手動インストールの流れ

1. リポジトリを取得し、`scripts/` と `.claude/hooks/stop-mvp-pipeline.sh` を使う場所へコピーする（例：`~/.claude/zunda-agent/`）。
2. コピーしたフック内の `REPO_ROOT` が、その配置のルートを指すようにする。
3. 下記のとおり `settings.json` に **Stop** フックを追加する。
4. （任意）VOICEVOX、[uv](https://github.com/astral-sh/uv)（rich 表示用）、tmux は環境に応じて入れる。

## Stop フック設定（グローバル）

`~/.claude/settings.json` にマージする例。パスを環境に合わせて置き換える。

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/Users/YOUR_USERNAME/.claude/zunda-agent/hooks/stop-mvp-pipeline.sh"
          }
        ]
      }
    ]
  }
}
```

## Stop フック設定（プロジェクトローカル）

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/stop-mvp-pipeline.sh"
          }
        ]
      }
    ]
  }
}
```

## 環境変数一覧

| 変数 | 効果 | デフォルト |
|------|------|------------|
| `ZUNDA_MUTE` | `1` のとき音声（TTS）のみスキップ | 未設定 |
| `ZUNDA_NOTIFY` | `0` のとき macOS 通知センターをスキップ | 未設定時は通知 ON |
| `ZUNDA_STOP_HOOK_CHILD` | 要約用子プロセスに付与（通常は手動設定しない） | — |
| `STOP_HOOK_LAST_MSG_FILE` | 最終メッセージ保存先のパス | `${TMPDIR}/claude-stop-last-assistant.txt` |
| `STOP_HOOK_JSON_LOG` | 設定時、受信 JSON をログに追記 | 未設定 |
| `ZUNDA_HOOK_INPUT_MAX_CHARS` | 要約に渡す入力の最大文字数 | `65536` |
| `ZUNDA_SUMMARY_MAX_LINES` | ローカル要約の最大行数 | `3` |
| `ZUNDA_SUMMARY_MAX_CHARS` | ローカル要約の最大文字数 | `200` |
| `ZUNDA_TTS_TEXT_MAX_CHARS` | TTS 直前マスク処理の入力上限 | `2000` |
| `ZUNDA_PANEL_LOG` | rich 表示が使う追記ログのパス | `${TMPDIR}/zunda-agent-panel.log` |
| `ZUNDA_HISTORY_COUNT` | パネルに出す直近件数（1〜100） | `5` |
| `VOICEVOX_HOST` | VOICEVOX Engine ホスト | `127.0.0.1` |
| `VOICEVOX_PORT` | VOICEVOX Engine ポート | `50021` |
| `VOICEVOX_OUT_WAV` | 合成 WAV の一時パス | `${TMPDIR}/zunda-agent-mvp-tts.wav` |
| `SAY_VOICE` | macOS `say` の声 | `Kyoko` |

## フォールバック動作

- **要約**: `claude` コマンドが無い、または要約に失敗 → `summarize_local.py` によるローカル要約
- **表示**: `uv` があれば rich パネル、それ以外は stderr に `[zunda] …`
- **読み上げ**: VOICEVOX に接続できない → macOS の `say`（`SAY_VOICE`）
