<p align="center">
  <img src="images/clustime_logo.png" alt="ClusTime ロゴ" width="200" />
</p>

# ClusTime

このリポジトリは、SNSアプリ「ClusTime」の設計と開発履歴をポートフォリオとしてまとめたものです。アプリ本体のソースコードは公開していません（技術的な構成・採用技術の共有が目的）。

## リンク
- App Store: https://apps.apple.com/jp/app/clustime/id6749456160
- Google Play: https://play.google.com/store/apps/details?id=com.clustime.clustime.beta&hl=ja

---

## 技術スタック（Tech Stack）
- UI フレームワーク: Flutter（Cupertino デザイン）
- バックエンド: Firebase
  - Authentication / Firestore / Cloud Functions / Storage / App Check / Crashlytics / Cloud Messaging (FCM)
- 状態管理: Riverpod（flutter_riverpod）
- 主要パッケージ:
  - firebase_core, firebase_auth, cloud_firestore, cloud_functions, firebase_storage
  - flutter_riverpod
  - cached_network_image（画像キャッシュ）
  - image_picker, image_cropper（画像選択・編集）
  - google_mobile_ads（広告）
  - timeago（相対時間表示）

## 🛠️ 設計思想と技術的ハイライト
本プロジェクトは、単に機能を実現するだけでなく、保守性・拡張性・堅牢性を重視した設計思想に基づいています。

### 1. アーキテクチャ：Feature-Driven Design × レイヤードアーキテクチャ
- 機能単位での関心事の分離: `lib/features` ディレクトリ以下に機能ごとのモジュールを配置する Feature-Driven Design を採用。機能追加や修正が他機能へ与える影響を最小化し、見通しを良くします。
- 依存関係の明確化: 各機能モジュールは `application`（ビジネスロジック）, `data`（データ層）, `presentation`（UI層）に分割。UI→ロジック→データの一方向依存を保ち、層ごとの独立テストや変更が容易です。

### 2. 状態管理：Riverpodによる宣言的なUI構築
- 依存性注入（DI）: Riverpod を全面採用し、Provider を介して Repository や Service を注入。ウィジェットツリーのどこからでも安全に依存性にアクセスでき、再利用性・テスト容易性を向上。
- 宣言的なデータフェッチ: `StreamProvider` や `FutureProvider` を活用し、Firebase との非同期通信やリアルタイム更新を宣言的に記述。ローディング・エラー・データ表示の分岐をシンプルかつ堅牢に実装。
- イミュータブルな状態: `freezed` により状態を不変に保ち、予期せぬ変更を防止して挙動を予測可能に。

### 3. バックエンド連携：Cloud Functions for Firebase の活用
- サーバーサイド・ロジック: 投稿作成・削除、ユーザー参加、権限譲渡など権限・安全性が重要な処理は Cloud Functions に集約。
- クライアントの責務軽減: クライアントはUI描画とユーザーインタラクションに集中。サーバー側へロジックをオフロードして、クライアントのシンプルさ・パフォーマンス・セキュリティを高めます。

### 4. 高い再利用性と一貫性を持つUIコンポーネント
- 共通コンポーネント: `lib/shared/widgets` に認証ボタン、ポップアップ、インジケーター等の再利用可能なコンポーネントを配置。重複を避け、一貫したUXを提供。
- テーマ管理: `lib/app/theme` にて配色やスタイルを一元管理（ダークモード対応）。UI変更や新テーマ追加が容易。

### 5. 開発と運用の効率化
- Flavor による環境分離: `main_prod.dart` と `main_beta.dart` を用意し、本番・ベータの Firebase プロジェクトやAPIキーを安全に切り替え。
- セキュリティ: Firebase App Check を導入し、不正クライアントからのアクセスをブロック。Crashlytics による障害把握。

## 📁 フォルダ構成（設計例）
プロジェクトは機能ごとに整理された `features` ディレクトリを中心に構成されています。

```
lib
├── app/                  # アプリ全体のコア設定（main_tab_screen, theme, providers）
├── core/                 # 横断的コア機能（config, firebase, services, utils）
├── features/             # 機能ごとのディレクトリ
│   ├── admin/            # 管理者向け機能
│   ├── auth/             # 認証関連
│   ├── cluster/          # クラスタ（コミュニティ）関連
│   ├── notifications/    # 通知関連
│   ├── post/             # 投稿関連
│   ├── profile/          # プロフィール関連
│   ├── report/           # 通報機能関連
│   └── search/           # 検索機能関連
├── shared/               # 共有Widgetやスタイル
│   ├── styles/
│   └── widgets/
├── main_beta.dart        # Beta版エントリーポイント
└── main_prod.dart        # Production版エントリーポイント
```

## 役割・体制（概要）
- 体制: エンジニア2名 + デザイナー1名
- 私の担当: フロントエンド全般（Flutter / iOS）、設計〜実装、UI コンポーネント、状態管理
- もう一名: Firebase Cloud Functions（投稿取得・プロフィール管理等のサーバー処理）

---

