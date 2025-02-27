# YuE-Enhanced: 優化版音樂生成系統

YuE-Enhanced 是原始 YuE 音樂生成系統的優化分支，針對音頻品質、生成穩定性和人聲一致性進行了重大改進。該系統使用二階段深度學習管道，從文本提示生成完整的歌曲，包含人聲和伴奏軌道。

## 🎵 功能特點

- **文本轉音樂生成**：從歌詞和音樂風格描述創建完整歌曲
- **雙軌輸出**：生成分離的人聲和伴奏軌道
- **音頻提示支援**：使用參考音頻引導生成風格
- **雙軌提示**：提供獨立的人聲和伴奏參考
- **高品質音頻**：可輸出工作室品質的 24 位 WAV 檔案

### ✨ 本分支的優化

- **人聲一致性增強**：智能 token 處理，保持一致的人聲特徵
- **自適應動態音軌平衡**：自動平衡人聲和伴奏電平，實現最佳混音
- **人聲共振峰增強**：改進人聲共振峰處理，使歌聲更自然
- **音高穩定化**：減少人聲中不自然的音高波動
- **平滑過渡**：段落間交叉淡入淡出，實現無縫音頻
- **強大的錯誤恢復**：Stage 2 推理中的自動重試機制
- **安全模式處理**：提供更保守的批次處理選項，防止記憶體問題
- **全面日誌記錄**：詳細日誌，便於問題排查

## 📋 系統需求

- Python 3.8+
- PyTorch 2.0+
- 支援 CUDA 的 GPU，建議 12GB+ VRAM
- 約 15GB 磁碟空間用於模型和依賴項

## 🔧 安裝

```bash
# 克隆此倉庫
git clone https://github.com/yourusername/yue-enhanced.git
cd yue-enhanced

# 安裝依賴
pip install -r requirements.txt

# 下載模型（或者首次運行時自動下載）
python download_models.py
```

## 💽 預訓練模型

YuE-Enhanced 使用兩個主要模型：
- Stage 1 模型：生成編碼序列（預設：`m-a-p/YuE-s1-7B-anneal-en-cot`）
- Stage 2 模型：將編碼序列轉換為高品質音頻（預設：`m-a-p/YuE-s2-1B-general`）

## 🚀 使用方法

### 基本用法

```bash
python infer.py --genre_txt genre.txt --lyrics_txt lyrics.txt --output_dir ./my_song
```

### 使用音頻提示

```bash
python infer.py --genre_txt genre.txt --lyrics_txt lyrics.txt --use_audio_prompt \
                --audio_prompt_path reference.mp3 --prompt_start_time 10 --prompt_end_time 40
```

### 使用獨立人聲和伴奏提示

```bash
python infer.py --genre_txt genre.txt --lyrics_txt lyrics.txt --use_dual_tracks_prompt \
                --vocal_track_prompt_path vocal.mp3 --instrumental_track_prompt_path instrumental.mp3
```

### 具有增強人聲的高品質輸出

```bash
python infer.py --genre_txt genre.txt --lyrics_txt lyrics.txt --high_quality \
                --enhance_audio --resonance_enhance --pitch_stabilize --adaptive_mixing \
                --vocal_consistency 0.3
```

## 📝 輸入格式

### 風格檔案 (genre.txt)
```
pop upbeat female_vocals bright energetic
```

### 歌詞檔案 (lyrics.txt)
```
[Verse1]
這是第一段歌詞
我美妙的歌曲
文字如流水般流動
伴隨著旋律播放

[Chorus]
這是副歌部分
最引人入勝的部分
音樂在這裡上升
觸動你的心
```

## ⚙️ 命令行參數

### 模型配置
- `--stage1_model`：Stage 1 模型檢查點（預設：`m-a-p/YuE-s1-7B-anneal-en-cot`）
- `--stage2_model`：Stage 2 模型檢查點（預設：`m-a-p/YuE-s2-1B-general`）
- `--max_new_tokens`：生成的最大新 token 數（預設：5000）
- `--repetition_penalty`：重複懲罰參數（預設：1.08）
- `--run_n_segments`：處理的段落數（預設：2）
- `--stage2_batch_size`：Stage 2 推理的批次大小（預設：4）
- `--stage2_overlap`：Stage 2 的重疊幀數（預設：100）

### 輸入選項
- `--genre_txt`：風格描述檔案路徑（必需）
- `--lyrics_txt`：歌詞檔案路徑（必需）
- `--use_audio_prompt`：啟用音頻提示
- `--audio_prompt_path`：音頻提示檔案路徑
- `--prompt_start_time`：音頻提示提取的起始時間（預設：0.0）
- `--prompt_end_time`：音頻提示提取的結束時間（預設：30.0）
- `--use_dual_tracks_prompt`：啟用雙軌提示
- `--vocal_track_prompt_path`：人聲軌檔案路徑
- `--instrumental_track_prompt_path`：伴奏軌檔案路徑

### 輸出配置
- `--output_dir`：輸出目錄（預設：`./output`）
- `--keep_intermediate`：保留中間檔案
- `--disable_offload_model`：生成後不將 Stage 1 模型從 GPU 卸載
- `--cuda_idx`：使用的 GPU 索引（預設：0）
- `--seed`：隨機種子（預設：42）
- `--high_quality`：啟用 24 位 WAV 輸出

### 音頻增強
- `--enhance_audio`：啟用音頻增強處理
- `--target_bw`：音頻編碼目標頻寬（預設：0.8）
- `--vocal_weight`：混音中人聲的權重（預設：0.55）
- `--vocal_consistency`：人聲一致性增強權重（預設：0.25）
- `--adaptive_mixing`：啟用自適應動態音軌平衡
- `--resonance_enhance`：啟用人聲共振峰增強
- `--pitch_stabilize`：啟用音高穩定化

### 故障排除選項
- `--debug_mode`：啟用詳細調試輸出
- `--stage2_retry`：啟用 Stage 2 自動重試
- `--safe_mode`：在安全模式下運行 Stage 2（單一批次處理）

## 🔄 處理流程概述

1. **Stage 1**：從文本和/或音頻提示生成編碼序列
   - 以歌詞和風格作為輸入
   - 可選擇使用音頻提示引導風格
   - 輸出編碼的音頻表示

2. **Stage 2**：將編碼序列轉換為高品質音頻
   - 分批處理 Stage 1 的輸出
   - 生成獨立的人聲和伴奏軌道
   - 應用音頻增強算法
   - 創建最終混合輸出

## 📊 性能說明

- 處理時間取決於輸入長度和系統規格
- 典型生成在高端 GPU 上需要約 5-10 分鐘
- 記憶體使用在 Stage 1 推理期間達到峰值（約 12GB VRAM）
- 在記憶體有限的系統上使用 `--safe_mode`
- 對於較長的歌曲，考慮增加 `--run_n_segments`

## 📈 優化詳情

### 人聲一致性增強

增強型人聲一致性處理器在生成的歌曲中保持更自然、一致的人聲特徵。它智能追蹤人聲模式並應用適當的權重，以維持音色一致性。

```bash
# 不同人聲風格的示例設置
# 對於穩定、一致的人聲：
--vocal_consistency 0.35 --resonance_enhance --pitch_stabilize

# 對於更動態、多變的人聲：
--vocal_consistency 0.15
```

### 自適應動態音軌平衡

此功能自動實時調整人聲和伴奏軌道之間的平衡，確保人聲在混音中始終適當出現，不被樂器聲壓過。

```bash
# 啟用方法：
--adaptive_mixing --vocal_weight 0.55
```

### 音頻品質增強

應用多種信號處理改進來增強音頻品質：
- 共振峰增強保持自然人聲音色
- 音高穩定化減少人聲中不必要的顫動
- 頻率特定處理提高清晰度和存在感
- 多頻段動態處理實現平衡、專業的聲音

## 🔨 故障排除

- **記憶體不足錯誤**：嘗試使用 `--safe_mode` 或降低 `--stage2_batch_size`
- **人聲質量不佳**：增加 `--vocal_consistency` 並啟用 `--resonance_enhance`
- **生成不穩定**：嘗試不同的 `--seed` 值或調整 `--repetition_penalty`
- **音頻瑕疵**：啟用 `--enhance_audio` 並嘗試調整 `--target_bw`

## 📜 授權

本項目基於 YuE，原始授權為 Apache 2.0。我們的修改也以 Apache 2.0 發布。

## 🙏 致謝

- 原始 YuE 開發者提供基礎模型和生成框架
- 本項目使用的音頻處理庫的貢獻者
- 開源 AI 和音頻社區

---

*本分支專注於提高音頻品質和生成穩定性。如有問題、建議或貢獻，請在 GitHub 上開啟 issue 或提交 pull request。*
