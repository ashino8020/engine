# 足の８０２０ Open API

**「歩く」を社会貢献として定量化する、オープンインターフェース。**

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/API-v1.0.0-blue.svg)](https://ashino8020.github.io/api)
[![Status](https://img.shields.io/badge/Status-Stable-brightgreen.svg)]()

---

## このAPIについて

「足の８０２０ウォーキング」は、80歳になっても自分の足で歩ける社会を目指す市民運動です（後援：厚生労働省・内閣府・デジタル庁・全国523自治体）。

このAPIを使うことで、あなたのアプリやサービスが「足の８０２０認定パートナー」として接続し、参加者の歩行データを受け付け、**医療費・介護費の削減推計額と社会貢献通知書**を提供できるようになります。

```
歩行記録 → APIで試算 → 「あなたは年間○万円分、社会に貢献しました」
```

---

## エンドポイント一覧

| メソッド | パス | 説明 | ステータス |
|---|---|---|---|
| GET | `/v1/calculate` | 社会貢献額を試算する | stable |
| POST | `/v1/records` | 歩行記録を登録する | beta |
| GET | `/v1/certificate` | 社会貢献通知書データを取得する | stable |
| GET | `/v1/city-report` | 自治体単位の集計レポートを取得する | beta |

**ベースURL:** `https://ashino8020.github.io/api/v1`

---

## クイックスタート

### 社会貢献額を試算する（5分でできます）

```javascript
// JavaScript / Node.js
const res = await fetch(
  'https://ashino8020.github.io/api/v1/calculate' +
  '?walking_days=22&avg_minutes=30&age_group=50s&region=urban&years=1',
  { headers: { 'Authorization': 'Bearer YOUR_PARTNER_KEY' } }
);
const data = await res.json();
console.log(`年間削減額: ${data.result.annual_saving_jpy.toLocaleString()}円`);
// → 年間削減額: 61,200円
```

```python
# Python
import requests
res = requests.get(
    'https://ashino8020.github.io/api/v1/calculate',
    params={'walking_days': 22, 'avg_minutes': 30, 'age_group': '50s'},
    headers={'Authorization': 'Bearer YOUR_PARTNER_KEY'}
)
print(res.json()['result']['annual_saving_jpy'])
```

---

## レスポンス例（`/v1/calculate`）

```json
{
  "status": "ok",
  "version": "1.0.0",
  "result": {
    "annual_saving_jpy": 72000,
    "cumulative_saving_jpy": 84960,
    "breakdown": {
      "medical_jpy": 44640,
      "care_jpy": 20160,
      "other_jpy": 7200
    },
    "achievement_rate": 0.81,
    "social_contribution_score": 82,
    "equivalent": {
      "school_meals": 314,
      "child_activity_days": 56
    }
  },
  "evidence": {
    "source": "MHLW-2023 / NCGG-SGS Cohort",
    "note": "参考推計値です。個人差があります。"
  }
}
```

---

## 試算ロジックの根拠

| データソース | 用途 |
|---|---|
| 厚生労働省「健康寿命延伸プラン」（2023年） | 歩行習慣と要介護リスク低減の相関 |
| 国立長寿医療研究センター NCGG-SGSコホート研究 | 年齢層別医療費削減推計値 |
| 内閣府「高齢社会白書」（2024年） | 健康寿命延伸の社会保障費削減効果 |
| 山形市「足の８０２０」実施効果測定（2025年） | 実参加者の歩数増加実績 |

年齢層別・地域別の基準削減額に、継続年数の複利係数・達成率係数・歩行時間ボーナスを乗じて算出します。試算値はすべて参考値であり、確定的な医療効果を保証するものではありません。

---

## パートナー認定制度

「足の８０２０認定パートナー」になることで、`partner_key` の発行・公式サイトへの掲載・共同プレスリリースの権利が得られます。

**認定条件：**
- 1日20分以上の歩行を記録・判定できること
- 参加者からAPIへのデータ送信について明示的な同意を得ていること
- 個人を特定する情報をAPIに送信しないこと
- 非営利・社会貢献目的であることをUI上で表示すること

**申請：** [ashino8020.jp](https://www.ashino8020.jp) のお問い合わせフォームから

---

## プライバシーと安全性

このAPIは個人情報保護法・GDPRの原則に従い設計されています。

- 送信できるのは**匿名化・ハッシュ化されたデータのみ**です
- 氏名・メールアドレス・住所・電話番号・生年月日の送信は**禁止**です
- 個人情報と思われるデータを検出した場合は `422 PII_DETECTED` を返します
- 足の８０２０が保持するのは自治体・年齢層・地域別の**集計値のみ**です

---

## 関連リンク

| リソース | URL |
|---|---|
| 公式サイト | https://www.ashino8020.jp |
| 社会貢献試算エンジン（一般向け） | https://ashino8020.github.io/engine |
| API仕様書（HTML版・詳細） | https://ashino8020.github.io/api |
| Instagram | https://www.instagram.com/ashino_8020 |
| GitHub | https://github.com/ashino8020 |

---

## ライセンス

MIT License — 商用・非商用を問わず自由にご利用いただけます。ただし「足の８０２０」のブランド・ロゴの使用は認定パートナーに限ります。

---

*一般社団法人 足の８０２０ — 「80歳になっても、逢いたい人に自分の足で逢いに行ける社会」へ*
