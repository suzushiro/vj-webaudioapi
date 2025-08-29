# Simple Audio Reactive Particles

TwitchでのDJ配信見据えて作った、音響反応型パーティクルビジュアライザーです。3Dパーティクルエフェクトを生成、リアルタイムの音声入力に反応して動きます。

![Demo](https://img.shields.io/badge/Demo-Live-green) ![Version](https://img.shields.io/badge/Version-1.0.0-blue) ![License](https://img.shields.io/badge/License-MIT-orange)

## ✨ 特徴

- **リアルタイム音声解析**: Web Audio APIによる音響処理
- **3Dパーティクルシステム**: Three.jsによる3Dオブジェクト表示  
- **音響反応**: 音量と低音域に特化した反応システム
- **カスタマイズ可能**: 感度とパーティクル数をリアルタイム調整
- **OBS連携対応**: ブラウザソースとして配信に組み込み可能
- **軽量設計**: シンプルな実装

## 🎵 音響反応の仕組み

| 音響要素 | 視覚効果 | 説明 |
|---------|---------|------|
| **音量** | パーティクル拡張・収縮 | 全体音量に応じて球体が伸縮 |
| **低音域** | 強い拡張 + サイズ変化 | ドラム・ベースに敏感に反応 |
| **周波数** | 色相変化 | 時間と音量に応じて色が変化 |
| **音量** | 透明度変化 | 音の大きさで明暗が変化 |

## 🚀 セットアップ

### 検証環境
- **OS**: Windows11,WSL1,Ubuntu (Mac/Linuxでも動くはず)
- **ローカルサーバー**: python3 (nodeでも可) 
- **ブラウザ**: Brave (Chromium/WebGL対応ならなんでも）
- **音声入力**: USBオーディオインターフェイスBerringer UCA222 (ブラウザで認識できるマイク装置ならなんでも)


### 1. ファイル準備
```bash
# リポジトリをclone
git clone git@github.com:suzushiro/vj-webaudioapi.git 
```

### 2. ローカルサーバー起動
```bash
# cloneしたリポジトリにcd
cd vj-webaudioapi

# Python経由でポート8000を使用してWebデーモンを起動
python -m http.server 8000

# (もしくはNode.js使用)  
npx http-server -p 8000
```

### 3. ブラウザでアクセス
```
http://localhost:8000/index.html
```

## 🎛️ 使用方法

### 基本操作

1. **オーディオデバイス選択**
   - ドロップダウンからUSBオーディオI/Fまたはマイク入力を選択

2. **音声入力開始**
   - 「🎤 Start Audio」ボタンをクリック
   - ブラウザの音声アクセス許可を承認

3. **パラメーター調整**
   - **Sensitivity (0.5-5.0)**: 音への反応の強さ
   - **Particle Count (500-5000)**: パーティクル数

### Virtual Audio Cable設定

DJ配信でライン入力を使用する場合：

```
USBオーディオI/F → ブラウザ 
                       ↘ OBS(配信)
```

1. **VoiceMeeter設定**
   - Hardware Input: USBオーディオI/F
   - Virtual Output: CABLE Input

2. **ブラウザ設定**
   - Audio Device: CABLE Output

3. **OBS設定**
   - ブラウザソース: `http://localhost:8000/audio-particles.html`

## 🎨 視覚的特徴

### パーティクルシステム
- **形状**: 円形グラデーションパーティクル
- **配置**: 球状分布（半径10-40単位）
- **数量**: 500-5000個（可変）
- **動作**: 音響による放射状拡張・収縮

### カメラワーク
- **自動回転**: ゆっくりとした軌道運動
- **距離**: 固定（50単位）
- **視点**: 常に中心を注視

### エフェクト
- **色相変化**: HSL色空間での滑らかな色変化
- **サイズ変化**: 低音に応じた動的サイズ調整
- **透明度変化**: 音量に応じた明暗効果

## 🔧 技術仕様

### 依存関係
- **Three.js r128**: 3Dレンダリングエンジン
- **Web Audio API**: リアルタイム音声解析
- **getUserMedia API**: 音声入力アクセス

### パフォーマンス
- **フレームレート**: 60FPS目標
- **音声解析**: FFT 256サンプル
- **レンダリング**: WebGLによるGPU加速

### 音声解析設定
```javascript
analyser.fftSize = 256;           // 高速処理用
analyser.smoothingTimeConstant = 0.8;  // スムージング
```

### 音楽ジャンル別推奨設定(未検証)

| ジャンル | Sensitivity | Particle Count | 特徴 |
|---------|-------------|----------------|------|
| **House/Techno** | 2.5-3.5 | 3000-4000 | 強い低音反応 |
| **Ambient/Chillout** | 1.5-2.5 | 2000-3000 | 滑らかな動き |
| **Drum & Bass** | 3.0-4.0 | 4000-5000 | 激しい反応 |
| **Jazz/Acoustic** | 1.0-2.0 | 1500-2500 | 繊細な表現 |

## 🛠️ カスタマイズ

### 色テーマ変更
```javascript
// 色相の変化速度調整
const hue = (time * 0.1 + this.volume) % 1;  // 0.1を変更

// 固定色に変更
this.particles.material.color.setRGB(1.0, 0.5, 0.8);  // ピンク系
```

### パーティクル分布変更
```javascript
// 立方体分布に変更
const x = (Math.random() - 0.5) * 60;
const y = (Math.random() - 0.5) * 60; 
const z = (Math.random() - 0.5) * 60;
```

### 反応感度調整
```javascript
// より敏感な低音反応
const bassRange = this.dataArray.slice(0, 4);  // 8から4に変更
```

## 🐛 トラブルシューティング

### よくある問題

**❌ 音声入力が動作しない**
```
解決方法:
1. ブラウザのマイク許可を確認
2. HTTPSまたはlocalhostでアクセス
3. オーディオデバイスの接続確認
```

**❌ パーティクルが表示されない**  
```
解決方法:
1. デベロッパーツールでThree.jsロード確認
2. WebGL対応ブラウザを使用
3. グラフィックドライバー更新
```

**❌ 反応が鈍い**
```
解決方法:  
1. Sensitivityを上げる（3.0以上）
2. Virtual Audio Cableの音量確認
3. 低音域の多い楽曲でテスト
```

### デバッグ情報
- **F12** → **Console**でログ確認
- **Volume/Bass**レベル数値を監視
- **Status**表示で動作状態確認

## 🔜 今後の予定

- [ ] 他の種類の実装、トランジション
- [ ] 複数音響パラメーターへの反応
- [ ] プリセット保存機能
- [ ] MIDIコントローラー対応
- [ ] フルスクリーンモード

## 📝 ライセンス

MIT License - 商用利用、改変、配布自由

---

**バージョン**: 1.0.0 