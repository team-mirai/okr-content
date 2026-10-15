# OKR コンテンツ

チームみらいの OKR 公開ページに表示するデータ。

## 構成

```
2026/                      ← 年
  q4/                      ← クォーター
    _meta.yml              期間・更新日(updated_at)・OKRについての補足
    party.yml              党全体の必達目標
    issues.yml             テーマ別の並び(必達目標 → グループ → KRのid一覧)
    hq/<本部id>/
      _hq.yml              本部の名前・並び順・説明
      o1.yml, o2.yml ...   1 Objective = 1ファイル
```

次のクォーターは `2027/q1/` のように、同じ形で追加する。
アプリは「最新の年 → 最新のクォーター」を自動で表示する(過去分の切り替えは将来対応)。

## 本部ID

| id | 本部 | id | 本部 |
|---|---|---|---|
| kocho | 広聴本部 | kaihatsu | 開発本部 |
| seicho | 政務調査会 | soshiki | 組織活動本部 |
| kokutai | 国会対策委員会 | jinji | 人事本部 |
| giin | 議員本部 | jimu | 事務本部 |
| koho | 広報本部 | senkyo | 選挙対策本部 |

## 本部ファイル (`hq/<本部id>/_hq.yml`)

| キー | 意味 |
|---|---|
| id | 本部のID(フォルダ名と同じ) |
| order | **画面での本部の並び順**(1から始まる数値。本部を足したら続きの番号を振る) |
| name / short / desc | 本部名 / 短い呼び名 / 説明文 |
| premise | ? 本部ごとの「前提・考え方」の説明。`title`(見出し)と `body`(Markdown) |
| hide_premise | ? `true` にすると premise を画面に出さない(データだけ残す) |

`premise` は組織別ビューで、その本部の見出しの下に折りたたみで表示される補足説明。

## Objective ファイル (`hq/<本部id>/o*.yml`)

| キー | 意味 |
|---|---|
| label | `O1` など |
| copy | Objective のサマリー(見出し) |
| objective | Objective の原文 |
| objective_list | ? 原文に続く箇条書き |
| krs | KR の一覧(下表) |

| KRのキー | 意味 |
|---|---|
| id | `<本部id>-o<番号>-kr<番号>`(例: `seicho-o1-kr1-1`)。同じ KR 番号が2つある場合は2つ目の末尾に `b` |
| k | 画面に出す KR 番号(`KR1`、`KR1-1` など) |
| text / summary | KR の原文 / 言い換え(サマリー) |
| notes | ? 補足の箇条書き |
| ctx | ? 注記 |
| metrics | 数値(c=実績, t=目標, u=単位, cmp=比較記号 `≦`/`≧`, l=ラベル)。複数指標なら複数並べる |
| goal_word | 定性目標語(`metrics` とどちらか一方だけ) |
| status | 達成・順調・注意・危険・未着手 |

### 進捗の書き方

- `status` が未着手の KR は、`c: 未着手` とし、目標(`t`)だけ数値で書く
- 実績がまだ出ていない KR は `c: 0`(単位は目標にあわせる)
- 「0件にする」のように少ないほど良い KR は `cmp: '≦'` を付ける(進捗バーは出ない)
- 進捗を更新したら `_meta.yml` の `updated_at` も更新する

## テーマ別の並び (`issues.yml`)・必達目標 (`party.yml`)

`party.yml` の `goals[].key` と `issues.yml` の `issues[].key` が対応する(`demo` / `open` / `org` / `talent`)。

| ファイル | キー | 画面での使われ方 |
|---|---|---|
| party.yml | label / full | 必達目標の短い名前 / 正式名 |
| party.yml | copy | 必達目標の見出し |
| party.yml | o / plain | 原文と、その箇条書き(「原文」表示のとき) |
| issues.yml | lead | テーマの見出しの下のリード文 |
| issues.yml | visual | `cycle`(サイクル図) または `index`(目次リンク) |
| issues.yml | groups[].tag / title | グループの見出し語 / 1行の説明 |
| issues.yml | groups[].krs | そのグループに並べる KR の id |
| issues.yml | groups[].sim | ? 内容の近い KR のまとまり(krs の中で隣り合わせる) |
| issues.yml | groups[].brief | ? `true` にすると補足の箇条書きと括弧書きを省いて表示 |

## ルール

- KRの `id` は変えない(issues.yml が参照する)
- issues.yml には全KRがちょうど1回ずつ出てくること
- 党内向けの情報(内部資料へのリンク、個人名、進捗の内部メモなど)は書かない

## 文章の書き方

文章系の項目(`objective` / `text` / `summary` / `notes` / `ctx.x` など)は
**Markdownの文字列**として書ける。リンクは `[表示する語](https://...)` と書く。

```yaml
notes:
  - 詳細は[みらい議会](https://example.com/gikai)を参照
```

- アプリ側は描画前にサニタイズする(リンクは http/https のみ許可)
- 注記 `ctx.a` の語とリンクの語は重ねない

## 検証

okr-app と okr-content を同じフォルダに並べて置き、okr-app の中で実行すると、公開時と同じ検証をローカルで実行できる。

```sh
cd okr-app
pnpm validate:content ../okr-content
```
