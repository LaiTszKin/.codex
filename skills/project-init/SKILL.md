---
name: project-init
description: Initialize new projects by gathering requirements, setting up a git repository, generating standard starter files (README, LICENSE, .gitignore, etc.), and making the first commit. Use when a user asks to start a new repo, scaffold a project, or set up initial files for a specific language and license.
---

# Project Init

## Overview
Initialize a new project repository with the required files and an initial commit. Ask clarifying questions first, then create files, initialize git, and commit.

## Workflow (順序執行)

### 1) 需求釐清（主動提問）
問清楚再動作，避免覆蓋既有內容：
- 專案路徑、專案名稱、簡短描述
- 使用語言（至少區分 `javascript` / `typescript`；可接受多語言）
- License 選擇：`MIT` / `Apache-2.0` / `GPL-3.0` / `UNLICENSED`
- 作者/組織名稱、年份（用於 License）
- 要建立的檔案（預設：`README.md`, `.gitignore`, `LICENSE`, `CONTRIBUTING.md`）
- 預設分支名稱（`main` 或使用者偏好）
- 是否要設定 git remote

### 2) 現況檢查
- 若目錄不存在，先建立目錄
- 若已有 `.git`：先確認是否要沿用或重建
- 若目錄內已有檔案：確認是否覆蓋或合併

### 3) 初始化 Git
- `git init`
- 視需要設定預設分支（例如 `git branch -M main`）

### 4) 建立必要檔案
- **README.md**：寫入專案名稱、描述、安裝/執行/開發指引（可先留 TODO）
- **LICENSE**：使用官方授權文本，填入年份與作者/組織名稱
  - 請用官方來源取得正確全文（避免手動拼寫）
  - 若選 `UNLICENSED`，改為建立 `UNLICENSED` 或不建立 `LICENSE`（需先問清楚）
- **.gitignore**：依語言套用模板（見 `references/gitignore-templates.md`）
- **CONTRIBUTING.md**：簡短貢獻流程（可先提供基本範本）

### 5) 初始提交
- `git add -A`
- `git commit -m "Initial commit"`
- 用 `git status` 與 `git log --oneline -1` 確認狀態

## 檔案範本指引

### README.md（最小範本）
- 專案名稱
- 一句描述
- 安裝 / 執行 / 開發指令（若未知，留 TODO）

### CONTRIBUTING.md（最小範本）
- Fork / Branch / PR 流程
- Code style（若未知，留 TODO）

## Resources

### references/
- `gitignore-templates.md`：常見語言的 `.gitignore` 範本（目前含 `javascript` / `typescript`）

