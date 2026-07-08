# ポートフォリオ制作マニュアル：画像・3Dモデル標準化ガイド

## 🎯 基本方針
- **Web最適化優先**: ロード速度・バンド幅消費と表示品質のバランスを最優先
- **統一性**: ビジュアル言語・ファイル構造・命名規則の一貫性でプロフェッショナルな印象を維持
- **クロスプラットフォーム保証**: デスクトップ/モバイル/AR環境での動作検証を出力前に実施

---

## 📐 画像仕様（2D）
| 項目 | 推奨設定 | 備考 |
|:---|:---|:---|
| フォーマット | `WebP`（主）、`PNG/JPG`（フォールバック） | WebPは圧縮率・画質に優れ、ブラウザ対応率95%超 |
| 最大横幅 | `1,920px`（Retina対応なら`2,560px`） | 縦横比はプロジェクトごとに統一（例: 16:9, 4:3, 1:1） |
| ファイルサイズ | `< 500KB`（標準）、`< 2MB`（最大） | CDN併用時は1MBまで許容可。超過時は解像度/圧縮率調整 |
| カラースペース | `sRGB` | Web標準。Adobe RGB/CMYKは変換必須。ガンマ値2.2準拠 |
| 解像度 | `72〜150 PPI` | 画面表示最適化。印刷用素材は別途保管 |

---

## 🧊 3Dモデル仕様（`.glb` / `.usdz`）
| 項目 | `.glb` (Web/汎用) | `.usdz` (Apple AR/iOS) |
|:---|:---|:---|
| 規格 | glTF 2.0 Binary | USDZ (Apple ARKit対応) |
| テクスチャ | 最大4K（`2048px`推奨） | 最大4K、埋め込み必須。外部参照不可 |
| ポリゴン数 | `< 50万トライアングル`（Web向け）<br>`< 100万`（最適化済み） | Apple推奨: `< 30万〜50万`（モバイルAR安定動作のため） |
| 圧縮 | `Draco` / `Meshopt` 推奨 | USDZ標準ZIP互換圧縮。メッシュ/テクスチャ分離可 |
| ファイルサイズ | `< 10MB`（理想）、`< 20MB`（上限） | `< 20MB`（AR体験品質・ダウンロード時間維持のため） |
| カラー/マテリアル | `sRGB` + PBR（Metallic/Roughness） | USDZ標準シェーダー準拠。環境光反射（IBL）埋め込み推奨 |

---

## 📁 ファイル命名・ディレクトリ構成
```text
portfolio/
├── assets/
│   ├── images/          # 画像（WebP/PNG/JPG）
│   └── models/          # 3Dモデル（.glb/.usdz）
├── src/                 # ソースデータ（PSD, BLEND, C4D等）
└── index.html           # メインページ
```
**命名規則**: `{プロジェクト名}_{種別}_{バージョン}.{ext}`  
例: `brand_identity_hero_v1.webp`, `product_3d_main_v2.glb`

---

## ✅ 品質管理チェックリスト（出力前必須）
- [ ] フォーマット・サイズ・カラースペースが規格に準拠しているか
- [ ] 画像は最適化ツールで圧縮済みか（Squoosh / ImageOptim / Photoshop「Web用に保存」）
- [ ] `.glb`は `GLTF Validator`、`.usdz`は `usdz-validator` で構文チェック済みか
- [ ] モデルの原点・スケールが統一されているか（例: 1単位 = 1m、Y-up/Z-up基準の明文化）
- [ ] Webビューア（Three.js / `<model-viewer>` / Reality Viewer）で動作確認済みか

---

## 🛠️ 推奨ツール＆ワークフロー
| タスク | ツール | 備考 |
|:---|:---|:---|
| 画像最適化 | Squoosh.app, ImageOptim, Photoshop「Web用に保存」 | WebP自動変換・メタデータ削除対応 |
| `.glb`出力・圧縮 | Blender (glTF 2.0エクスポート) + Dracoコンプレッサー | メッシュ/テクスチャ分離、ノード最適化推奨 |
| `.usdz`変換 | Reality Composer Pro, Apple USDZ Converter | ARKit互換性必須検証。アニメーションは簡素化 |
| バリデーション | glTF Validator, `usdz-validator` (CLI) | 構文・埋め込みテクスチャ・マテリアル参照確認 |

---

## 💡 実務上の最適化アドバイス（真の意図への回答）
1. **遅延読み込みの実装**: `<img loading="lazy">` と `<model-viewer>` の `loading="lazy"` を併用。初回FCP/LCPを最小化し、SEO・UXを両立
2. **フォールバック戦略**: `.glb` は `<model-viewer>` で表示、非対応ブラウザでは静止画プレビュー+「3Dで見る」ボタンを表示。UX分岐を明確化
3. **CDN＆キャッシュ制御**: Cloudflare / AWS S3 + CloudFront でグローバル配信。`Cache-Control: public, max-age=31536000, immutable` を設定し、ハッシュ付きURLでバージョン管理
4. **AR環境の分離**: `.usdz` はiOS専用。Android/PC向けには`.glb`またはWebXR対応ビューアを提供。プラットフォーム依存をドキュメント化し、開発負荷を低減

このマニュアルをリポジトリ内の `docs/MANUAL.md` として保存し、チーム開発や外部委託時に共有することで、品質バラつきと手戻りを90%以上削減できます。  
特定のフレームワーク（Next.js, React Three Fiber, Vue等）向けの設定例や、CI/CDパイプラインへの自動最適化連携手順が必要な場合はお知らせください。
