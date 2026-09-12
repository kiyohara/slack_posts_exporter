# 作業ブランチメモ

- ブランチ: adopt-note-numbering-and-pr-tool-name-updates
- PR: #225
- 最終更新: 2026-09-12

## 目的

Issue #224。類似プロジェクト asahimaru の PR #725 / #726 で入った AI agent 向け skill / rule 更新を slapex へ取り込む。

- asahimaru/asahimaru#725: `number-working-branch-note` skill の stale 検出範囲に「本 skill の実行で完了するタスク行」を加える。
- asahimaru/asahimaru#726: PR の tool 名記載禁止を PR title のみに限定する。

## 現在の状況

- `.agents/skills/number-working-branch-note/SKILL.md` の「stale 表現の定型置換」節を 2 種類の検出対象に再構成し、Step 5 / Step 10 /「やらないこと」を同期した。
- `doc/guidelines/pull-request-guidelines.md` の「基本方針」と「Tool 名の扱い」を title 限定へ改訂し、旧例外条項を削除した。
- 正本要約を持つ skill 2 本(`number-working-branch-note` / `release`)を追随させた。
- decision log `0057-pr-tool-name-restriction-scope.md` を追加し、`index.md` に行を足した。

## 決定事項

- asahimaru 版のユーザー確認 gate は取り込まない。slapex は commit 73a4deb「Remove user gates from branch note numbering」と 48377e0「Define stale branch note replacements」で確認 gate を外し、定型置換表による機械置換へ寄せた経緯がある。取り込むのは検出範囲の拡張だけとし、曖昧な行は触らず終了時に報告する既存の扱いに合わせた。
- 非 checkbox 行の書き換えは「行末に `(完了)` を付ける」の 1 通りに決めた。asahimaru 版は「削除するか `(完了)` を付ける」の両方を許容しているが、slapex の定型置換方針では分岐を残さない。また note は作業ログであり、採番時点で何が完了していたかを消さずに残す方が後から辿れる。
- slapex の既存 note は checkbox を使わず `- ` の箇条書きか番号付きリストである。checkbox の行は現状存在しないが、記法が混ざったときに判断が割れないよう表には両方を残した。
- decision log は tool 名の範囲変更(項目 2)についてのみ作成した。項目 1 は skill の検出範囲の調整であり、方針の変更・撤回にはあたらないため作らない。
- 既存 note に残る stale 行(`5_` / `9_` / `10_` / `11_` / `12_` / `13_` / `14_` / `58_` / `185_`)は修正しない。`working-branch-notes-handling.md` のメンテコスト判断に従い据え置く。asahimaru 側も同じ扱い。
- `.cursor/rules/` と `.claude/rules/` の PR guideline 入口 shim は正本へのポインタのみで禁止内容を持たないため変更不要と判断した。
- review 指摘(P2)を受け、完了タスク行の例から「採番後に `progress.md` の PR 番号を反映する」を削除した。本 skill は Step 7 で commit 対象を `working-branch-notes/` 配下に限定しており、`progress.md` は更新しない。この例を残すと、実際には未更新のタスクへ `(完了)` を付けることになる。
- あわせて、1 行に本 skill で完了する要素と完了しない要素が混在する行(`58_v1-16-final-e2e.md:25` の「PR を作成し、採番後に note rename と `progress.md` の PR 番号反映を行う」など)は行全体を完了扱いにせず、触らずに報告する扱いを明記した。未完了部分が隠れるのを防ぐため。

## 次にやること

- review 指摘への対応。
- merge はユーザーが行う。

## 検証

文書と skill の変更のみで、Go code の変更は無い。アプリの test は対象外。

- `git grep "tool 名"` の結果を確認し、PR guideline 由来の旧表現(title / description 両方を禁止する記述)が残っていないことを確認した。MCP server の tool 名を指す `doc/guidelines/github-mcp-guidelines.md` と `.agents/mcp/github-op-integrated/README.md` は別文脈のため対象外。
- 取り込み対象の 2 PR は asahimaru の master に merge 済みで、追加の review 修正 commit が無いことを確認したうえで最終内容を参照した。
- 本 Issue の背景として挙げた既存 note の stale 行は、`working-branch-notes/` に対する grep で実在を確認した。特に `185_asset-extension-from-content.md` は採番時点で「PR 作成後に note を `<PR 番号>_asset-extension-from-content.md` へ rename する」が残っており、placeholder 表記を取りこぼす現行手順の実例である。
- `doc/guidelines/document-style-guidelines.md` の「絵文字は使わない」に対しては、`🤖 Generated with ...` を trailer の実文字列として引用する箇所にのみ使用しており、文書のトーン装飾としては使っていない。

## リスク・ブロッカー

- 検出対象を広げた分、採番 skill が書き換える行が増える。誤検出の懸念に対しては、「本 skill の実行で完了したか判断できない行は触らず報告する」という共通の扱いで吸収する。
- description の tool 名を制限しなくなるため、書き方のばらつきは許容する。実運用で問題が出た時点で 0057 の「後から見直す条件」に従って再検討する。

## セッションログ

- 2026-09-12: asahimaru の PR #725 / #726 を調査し、Issue #224 を作成。skill / guideline / decision log の変更を実施した。
- 2026-09-12: Codex review(review cycle `codex-15d49ea-20260912023609`)の P2 指摘 1 件に対応し、完了タスク行の対象から `progress.md` 更新を除外、複合行の扱いを明記した。
