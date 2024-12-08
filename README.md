<h1 align="center">Hi 👋, I'm Masafumi</h1>
<h3 align="center">A Software Engineer from Japan.</h3>


- 🔭 I’m currently working on [TuneCore Japan](https://www.tunecore.co.jp/)

- 🌱 I’m currently learning **software design, Go, DDD, typescript, React.**

- 📝 I regularly write articles on [https://qiita.com/masafumi330](https://qiita.com/masafumi330)

- 💬 Ask me about **Go, Clean Archetecture and DDD.**

- 📫 How to reach me **https://twitter.com/masafumi330**

<details><summary>参画プロジェクト内容（最終更新：2024/12/8）</summary>

# 【正社員】アーティスト向け音楽流通サービス「TuneCore Japan」の開発・運用
## プロダクト概要
- TuneCore Japan は、世界最大手の音楽のディストリビューション会社 「 TuneCore,Inc.（米国）」 とWano株式会社（日本）の合弁企業（ジョイント・ベンチャー）で、2012年にサービスを開始した、**アーティスト向けの音楽流通サービス**を提供する会社です。
- 誰でも簡単に自分の楽曲を世界中のストアで販売・配信でき、**iTunes Store や Apple Music、Spotify、YouTube Music など、国内外の音楽配信プラットフォームへの楽曲配信・管理を一括で行うことができるツール**として、数多くのアーティストやレーベルが利用しています。
- サービス開始以来、登録アーティスト数及び配信楽曲数（＝流通量）は増加を続け、年間の邦楽デジタル音楽流通量は日本一を達成。また、2023年までのアーティストへの累計還元額は**547億円**を突破しました。
-「あなたの音楽でセカイを紡ぐ」のビジョンのもと、アーティストをサポートし、アーティストおよび音楽の素晴らしさを世界中に広めるサービスとして積極的に事業展開をしています。
- URL: https://www.tunecore.co.jp/

## プロジェクトの規模
プロダクト開発チーム 18名
・プロダクトチーム　3名（GM 1名、PM　1名、Webディレクター　1名）
・デザインチーム　3名（アートディレクター　1名　業務委託デザイナー　2名）
・エンジニアチーム　12名（フロントエンド　4名　バックエンド　8名）

## 技術スタック

|                |                              技術スタック                                                    | 
| -------------- | -------------------------------------------------------------------------------- | 
| Frontend       | React, Typescript, Next.js, Tailwind CSS, node.js, vite, recoil, React Hook Form, jQuery | 
| Backend        | Go, Echo, Perl, Ruby                                                             | 
| DB             | MySQL                                                                            | 
| Infrastructure | AWS, Docker                                                                      | 
| 開発支援ツール | GitHub, Notion, Slack                                                                          | 

## 業務内容
- TuneCore Japan ユーザー画面、ストア向け楽曲納品サーバー・ワークフロー、アーティストの楽曲審査や管理に使用する社内管理画面の開発・運用
  - 仕様定義
  - 開発工数の見積もり
  - PdMや他部署の機能開発要求者との、仕様、スコープの調整
  - 小~中規模（開発工数 ~ `1人月` 程度)の機能開発のアーキテクチャ設計
  - API設計
  - DBスキーマ設計
  - Webフロントエンド実装 (React, Typescript, jQuery)
- プロダクトの運用保守, Bugの原因特定・修正対応
- コードレビュー
- 継続的なリファクタリング、ドキュメント作成によるチームへの知識還元
 

### API設計・開発
#### Perl製API -> Go製APIへのモダン化
- 課題
  - チームの方針の一つとして、Goへとモダン化を進めていくことがあった
  - ただし、プロダクトとして常に新しい価値を提供していくことが求められるため、**新規開発を止めてまでリファクタリングの時間を取ることはできない**
- 解決方法
  - 新規開発は行いつつ、モダン化できる部分から**スモールステップ**でGo製APIへ移行する開発を行った
- 工夫点
  - Frontendの画面開発について、新規ページの開発となったため、Perl製APIはそのままで、Goで新規APIを実装し、新規ページから新規APIへリクエストを行うようにすれば、サービスを止めずにモダン化ができると判断した。そのための事前調査を行い、**事前に入念にローンチ計画を立て**、PdMとも議論を行った。

### アーキテクチャ設計
#### 既存楽曲をコピーする機能の開発
- 課題
  - 楽曲を多数保有しているようなユーザーの場合、すでに配信登録している楽曲をコピーして、別のアルバムにも同楽曲を登録したいというニーズがあった。
  - ただし、楽曲情報を1から新規作成して登録する操作が多く、同じ楽曲を別の複数のアルバムに登録することはなかった。
  - ビジネス観点からも、既存楽曲をコピーすることで新しいアルバムを作成・登録してもらうことで、さらなる利益も見込まれるため、優先度が高い開発案件だった。
- 解決方法
  - 既存楽曲の検索モーダル及び、既存楽曲の選択・保存機能を開発した。
- 工夫点
  - 元楽曲の情報の編集を行った際、コピーした楽曲への編集内容を即時同期するため、イベント駆動アーキテクチャを採用した。
    1. 「元楽曲の情報の編集」を行うと、編集内容をJobキューにEnqueueする
    2. 同期処理を行うためのWorkerプロセスが常に走っており、このプロセスがJobキューからDequeueする
- 成果
  - コピー機能による新規アルバム登録数が増加し、開発コスト `300万` に対して、約一年で `70%(210万)` のコスト改修が達成できた。

### パフォーマンス改善
#### React製編集画面からの冗長なAPIリクエスト回数を `50%` 削減。前月比 `6,000リクエスト` の削減▼
- 課題
  - ある特定機能の編集画面を開くと、同一APIに対して複数回リクエストを叩いていることを、ブラウザの検証ツールにて発見した。
  - 複数のコンポーネントがそれぞれ独自に、同一のカスタムフックを利用しているため、発生していた。
  - 幸い、致命的なエラーは発生していなかったが、意図しない挙動が発生する可能性、冗長なリクエストによる、本来不要なサーバーのリソース消費が懸念となっていた。
- 解決方法
  - 複数のコンポーネントの共通の親コンポーネントからのみ、特定のカスタムフックを利用し、その子コンポーネントに対しては、Propsでデータを受け渡すようにした。これにより、カスタムフックに依存するのは、親コンポーネント1つのみとなった。
- 工夫点
  - `useSwr` などのように、リクエストをキャッシュするライブラリツールもあるが、まずは基本に忠実に、親コンポーネントへ`リフトアップ`し、Propsとして子コンポーネントにデータを渡すという、正攻法で改善を行った。
- 成果
  - 修正の結果、編集画面を開いた際の、同一APIに対して複数回リクエスト回数を**50%削減**することができた。月合計だと、**前月比 6,000リクエストの削減▼**となった。

### 開発プロセス改善
#### CodeBuild CI実行時間の `50%` 削減
- 課題
  - Goレポジトリにおける、テスト環境・本番環境にマージ時のCI実行時間が `10分強` を超えていた
  - 特にテスト環境においては、早期にテスターからのフィードバックを受けたいニーズが高く、そのため、テスト反映のサイクルを高速化する必要があった。
- 解決方法
  - 調査検証を行い、以下の対策を行った
    - CudeBuildのローカルキャッシュを設定し、キャッシュヒットした際には、依存モジュールのダウンロードはキャッシュを用いて高速化した
    - 既にCI実行時のCPU使用率が `90%` を超えている状態だったため、CPUリソースの枯渇により、CI実行に時間が掛かっていると仮説を立て、プロビジョニング環境のグレードアップを行った

- 工夫点
  - 安易にスペックのグレードアップを図るのではなく、まずは設定や不要な処理の見直しを行うことで、解決を図ったこと。
  - 
- 成果
  - CI実効時間の `50%` 短縮を達成
  - グレードアップにより単位時間当たりの料金は `2倍` になったが、実効時間が `2分の1`に短縮できたため、結果として総コストに大きな変化はなく、CI実効時間の短縮を達成できた
</details>

