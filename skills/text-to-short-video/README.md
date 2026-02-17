# text-to-short-video

把文章、腳本、章節、筆記轉成 30–60 秒短影片的 Codex Skill。

此 Skill 會：

- 從文字抽出可視化場景
- 以 `openai-text-to-image-storyboard` 的 JSON schema 產生 `prompts.json`
- 生成圖片並組裝成 Remotion 影片
- 在輸出後自動檢查比例與尺寸，必要時執行中心裁切與縮放

## 依賴 Skills

- `openai-text-to-image-storyboard`
- `remotion-best-practices`

## 環境設定

1. 複製環境範本：

```bash
cp /Users/tszkinlai/.codex/skills/text-to-short-video/.env.example \
   /Users/tszkinlai/.codex/skills/text-to-short-video/.env
```

2. 至少填入：

- `OPENAI_API_URL`
- `OPENAI_API_KEY`

3. 可用以下設定預設輸出尺寸：

- `TEXT_TO_SHORT_VIDEO_WIDTH`
- `TEXT_TO_SHORT_VIDEO_HEIGHT`

## 比例修正（後處理）

渲染完成後，請執行：

```bash
python /Users/tszkinlai/.codex/skills/text-to-short-video/scripts/enforce_video_aspect_ratio.py \
  --input-video "<rendered_video_path>" \
  --output-video "<final_output_video_path>" \
  --env-file /Users/tszkinlai/.codex/skills/text-to-short-video/.env \
  --force
```

行為如下：

- 若輸出比例不符目標尺寸：先中心裁切再縮放
- 若比例相同但解析度不同：直接縮放
- 若比例與解析度都相同：保持原始內容（no-op/copy）

## 檔案結構

```text
text-to-short-video/
├── SKILL.md
├── README.md
├── LICENSE
├── .env.example
├── agents/
│   └── openai.yaml
└── scripts/
    └── enforce_video_aspect_ratio.py
```

## License

本專案採用 [MIT License](./LICENSE)。
