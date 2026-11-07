---
name: backlog-refiner
description: 檢查 GitHub Project 看板 Ready 欄位中的 work item（Issue），若缺少 User Story 或 Acceptance Criteria，就依標題與專案內容自動撰寫並更新到 Issue 內文。用於需求管理、Backlog 整理、Backlog Refinement。
---

# Backlog Refiner

## 何時使用
- 使用者要求「檢查 Ready 欄位」「補齊 User Story / Acceptance Criteria」「整理 backlog」。
- 新增了 backlog item，只有標題，需要產生需求內容。

## 流程

1. **取得清單**：列出 Project 看板中 Status = `Ready` 的 Issue。
   - 優先使用 GitHub MCP / `gh`：
     `gh project item-list <PROJECT_NUMBER> --owner <OWNER> --format json`，篩選 `status == "Ready"`。
   - 若無法讀取 Project，退而求其次：`gh issue list --state open`，並請使用者確認哪些在 Ready。
2. **逐一檢查**：讀取每個 Issue 內文（`gh issue view <N> --json title,body`）。
   - 內文已含 `## User Story` **且** `## Acceptance Criteria` → 跳過，不要改動。
   - 缺少其中任一項 → 進入撰寫。
3. **理解脈絡**：閱讀 `README.md`、`app.py`、`templates/`、`tests/`，了解專案（BMI 計算器：Flask、輸入身高體重、顯示 BMI 與體位分類），讓內容貼近現有功能。
4. **撰寫內容**（只依標題與專案脈絡推論，不要捏造無關功能）：
   - **User Story**：一句話，格式 `身為 <角色>，我想要 <功能>，以便 <價值>。`
   - **Acceptance Criteria**：3–5 條，可驗證、可測試，使用 Given / When / Then；至少包含 1 條正常情境與 1 條錯誤／邊界情境。
5. **更新 Issue**：保留原本內文，在後面附加缺少的區塊（`gh issue edit <N> --body-file <file>`）。不得更改標題、標籤、Status 欄位。
6. **回報**：列出「已更新」與「已略過」的 Issue 編號及原因。

## 內文格式

```markdown
## User Story
身為 <角色>，我想要 <功能>，以便 <價值>。

## Acceptance Criteria
- [ ] Given <前置條件>，When <操作>，Then <預期結果>
- [ ] Given <前置條件>，When <操作>，Then <預期結果>
- [ ] Given <異常或邊界條件>，When <操作>，Then <預期的錯誤處理>
```

## 規則
- 使用繁體中文撰寫。
- 一個 Issue 只描述一個功能，不擴大範圍。
- 內容要具體到可寫成測試（例如明確數值、畫面文字），避免「使用者體驗良好」這類無法驗證的敘述。
- 不確定的需求，在 Issue 末尾以 `> 待確認：...` 標註，不要自行假設。
