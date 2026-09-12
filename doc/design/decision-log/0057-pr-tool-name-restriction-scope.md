# 0057 PR の tool 名記載禁止を title のみに限定する

- 状態: decided
- 作成日: 2026-09-12
- 最終更新日: 2026-09-12
- 関連: `../../guidelines/pull-request-guidelines.md`, [0048-document-style.md](0048-document-style.md)

## 背景

`doc/guidelines/pull-request-guidelines.md` は当初から、PR title と PR description の両方について「作成に使った tool 名やそれを示す prefix を含めない」と定めていた。加えて、変更対象の実ファイルパス(`.cursor/rules/` など)は例外として記載してよい、という条項を持っていた。

この方針は、レビュアーにとって重要なのは「どの tool で作ったか」ではなく「プロジェクトにどういう変更を入れるか」である、という考え方に基づく。

一方で運用上は次の摩擦が出ていた。

- 多くの AI coding agent が `Co-Authored-By` trailer や `🤖 Generated with ...` 形式の trailer を description へ付ける既定動作を持ち、そのたびに禁止ルール側を優先して除去する判断が必要になる。
- 例外条項があることで、「どこまでが tool 名で、どこからが単なるファイルパスか」の線引きを都度考える必要があった。

類似プロジェクト(asahimaru)で同じ問題に対して禁止対象を title のみへ限定する変更([asahimaru/asahimaru#726](https://github.com/asahimaru/asahimaru/pull/726))が入り、slapex でも同じ整理を採る。

## 候補

1. 従来どおり title / description の両方で禁止する。
2. 禁止対象を title のみに限定し、description と PR コメントは制限しない。
3. 禁止をすべて撤廃する。

## 検討内容

- 候補 1 は、AI agent の既定動作と恒常的にぶつかる。除去し忘れの検出にレビューコストがかかる一方、得られるのは description の体裁の統一だけである。
- 候補 3 は、PR 一覧で title に `[codex]` のような prefix が並ぶ状態を許すことになる。title は Issue 一覧・PR 一覧・merge commit・release note に繰り返し現れるため、ここだけは変更内容の記述に使い切りたい。
- 候補 2 は、ガードレールを最も効く場所(title)に残しつつ、description の記述は書き手の裁量に任せられる。trailer の扱いも明示できる。
- 例外条項(実ファイルパスの記載可)は、禁止対象が title の tool 名・prefix に限定されれば文面から自明になり、行として残す必要がなくなる。

## 決定

PR の tool 名記載禁止は **PR title のみ** を対象とする。

- PR title には `codex`、`claude`、`cursor` などの tool 名、および `[codex]` のような tool 由来の prefix を含めない。
- PR description と PR コメントでは制限しない。`Co-Authored-By` や `🤖 Generated with ...` のような trailer も記載してよい。
- trailer 以外の表現については禁止も許可も明言せず、書き手の裁量に任せる。
- 旧ルールの例外条項(実ファイルパスとしての `.cursor/rules/` などの記載可)は削除する。

正本は `doc/guidelines/pull-request-guidelines.md` に置く。

## 理由

- title はレビュアーが変更内容を一覧で把握するためのものであり、ガードレールとしてはここだけで足りる。
- description に何を書いてよいかを細かく規定すると、`agent-configuration-management.md` の「ルールをシンプルに保つ」方針に反して条件分岐が増える。仮想シナリオへ先回りせず、実運用で問題が出た時点で見直す。
- commit message 側(`doc/guidelines/git-operation-guidelines.md`)は元々 tool 名について無規定であり、description を制限しないことで PR と commit の扱いの差も小さくなる。

## 影響

- `doc/guidelines/pull-request-guidelines.md` の「基本方針」と「Tool 名の扱い」を改訂する。
- 正本要約を持つ skill を追随させる。`.agents/skills/number-working-branch-note/SKILL.md`(参照する正本・Step 10)と `.agents/skills/release/SKILL.md`(参照する正本)。
- `.cursor/rules/` と `.claude/rules/` の PR guideline 入口 shim は正本へのポインタのみで禁止内容を持たないため、変更しない。
- 既存 PR の description は遡って修正しない。
- description の体裁は裁量任せになるため、書き方のばらつきは許容する。

## 後から見直す条件

- description に tool 由来の定型文が増え、レビューの可読性を実際に損なっていると判断できる事例が出た場合。
- title 側の禁止だけでは防げない形で、tool 名が PR の見え方を占有するようになった場合。
