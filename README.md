# AI-AGENT

# 🎖️ CMO 系統 - 完整部署指南

## 📦 系統概覽

此 CMO (Command Modern Operations) 兵推系統，整合了：
- ✅ Ollama + Llama3-8b LLM
- ✅ 4 個 CMO API 功能
- ✅ 完整的前端界面
- ✅ Mock 和真實 API 雙模式

---

## 📂 文件結構

```
cmo_system/
├── excel_parser.py                      # EXCEL 解析器
├── function_definitions_complete.json   # Function Definitions
├── cmo_api_client.py                   # CMO API 客戶端
├── ollama_client_enhanced.py           # Ollama 客戶端
├── cmo_backend_complete.py             # FastAPI 後端
├── requirements.txt                     # Python 依賴
├── 中科院API對接文檔.md                 # API 對接指南
├── templates/
│   └── index.html                      # 前端界面（你提供的）
└── static/
    └── maps/                           # 地圖輸出目錄
```

---

## 🚀 快速開始

### 步驟 1: 準備環境

```bash
# 1. 創建項目目錄
mkdir cmo_system
cd cmo_system

# 2. 放置所有文件到此目錄

# 3. 安裝 Python 依賴
pip install -r requirements.txt
```

### 步驟 2: 準備 EXCEL 文件

將 4 個 EXCEL 文件放在與 `excel_parser.py` 同一目錄：
- API_import_scenario輸入與輸出範例.xlsx
- API_start_scenario輸入與輸出範例.xlsx
- API_get_wta輸入與輸出範例.xlsx
- API_get_answer輸入與輸出範例.xlsx

### 步驟 3: 解析 EXCEL

```bash
python excel_parser.py
```

應該看到：
```
✅ import_scenario: 成功解析 12 筆資料
✅ start_scenario: 成功解析 1 筆資料
✅ get_wta: 成功解析 7 筆資料
✅ get_answer: 成功解析 1 筆資料
✅ Function Definitions 已保存
```

### 步驟 4: 準備前端

將你的 `index.html` 複製到 `templates/` 目錄：
```bash
cp /path/to/your/index.html templates/
```

### 步驟 5: 配置模式

編輯 `cmo_backend_complete.py`，選擇運行模式：

#### 模式 A: Mock 模式（推薦先測試）
```python
# 第 118-126 行
llm_client = create_llm_client(
    use_ollama=False,  # ← False
    use_mock=True,     # ← True
    ...
)

# 第 129-134 行
cmo_client = CMOAPIClient(
    ...
    use_mock=True,     # ← True
    ...
)
```

#### 模式 B: Ollama + Mock API
```python
# 第 118-126 行
llm_client = create_llm_client(
    use_ollama=True,   # ← True (需要先安裝 Ollama)
    use_mock=False,    # ← False
    ...
)

# 第 129-134 行
cmo_client = CMOAPIClient(
    ...
    use_mock=True,     # ← True
    ...
)
```

#### 模式 C: Ollama + 真實 API（生產環境）
```python
# 第 118-126 行
llm_client = create_llm_client(
    use_ollama=True,   # ← True
    use_mock=False,    # ← False
    ...
)

# 第 129-134 行
cmo_client = CMOAPIClient(
    api_base_url="http://中科院API地址",  # ← 填入真實 URL
    api_key="你的API Key",              # ← 如需要
    use_mock=False,    # ← False
    ...
)
```

### 步驟 6: 啟動系統

```bash
python cmo_backend_complete.py
```

應該看到：
```
============================================================
CMO 系統啟動 - 完整版
============================================================
工作目錄: /path/to/cmo_system
前端界面: http://localhost:8000/
API 文檔: http://localhost:8000/docs
============================================================
INFO:     Uvicorn running on http://0.0.0.0:8000
```

### 步驟 7: 訪問系統

在瀏覽器打開：
```
http://localhost:8000/
```

---

## 🎯 功能測試

### 功能一：兵棋場景匯入 (import_scenario)

**測試步驟**:
1. 點擊左側「功能一：兵棋場景匯入」
2. 在對話框輸入：`繪製解放軍054A和055的座標`
3. 點擊發送

**預期結果**:
- 對話框顯示：「已繪製解放軍054A和055的座標」
- 右側地圖顯示 054A 和 055 的位置標記

**其他測試命令**:
- `標示中國船艦052D的座標`
- `繪製我方PGG的座標`
- `匯入兵推場景`（顯示全部單位）

### 功能二：啟動模擬 (start_scenario)

**測試步驟**:
1. 點擊左側「功能二：啟動模擬」
2. 輸入：`開始模擬`
3. 點擊發送

**預期結果**:
- 對話框顯示：「進行模擬中」
- 系統開始輪詢模擬狀態
- 5 秒後顯示：「武器分派記錄共 10 筆」

### 功能三：武器分派 (get_wta)

**測試步驟**:
1. 點擊左側「功能三：武器分派」
2. 輸入：`如何攻擊052D`
3. 點擊發送

**預期結果**:
- 對話框顯示：「武器分派結果如下」
- 顯示 WTA 表格
- 地圖顯示攻擊箭頭

### 功能四：RAG 問答 (get_answer)

**測試步驟**:
1. 點擊左側「功能四：RAG 問答」
2. 輸入：`何謂交戰規則？`
3. 點擊發送

**預期結果**:
- 對話框顯示 AI 回答（非串流模式）
- 或逐字顯示回答（串流模式）

---

## ⚙️ 配置說明

### Ollama 配置

如果使用 Ollama：

1. **安裝 Ollama**
   - Windows: 下載 https://ollama.com/download/windows
   - macOS/Linux: `curl -fsSL https://ollama.com/install.sh | sh`

2. **下載模型**
   ```bash
   ollama pull llama3
   ```

3. **啟動 Ollama**
   - Windows: 自動啟動
   - macOS/Linux: `ollama serve`

4. **測試連接**
   ```bash
   curl http://localhost:11434/api/tags
   ```

### 中科院 API 配置

**在對接真實 API 前**，請先閱讀 `中科院API對接文檔.md`，確認：

1. ✅ API Base URL
2. ✅ API 認證資訊
3. ✅ 所有端點路徑
4. ✅ 請求/回應格式
5. ✅ 錯誤處理
6. ✅ 測試環境

**配置文件** (可選)：

創建 `config.json`：
```json
{
  "cmo_api": {
    "base_url": "http://api.ncsist.org.tw:8080",
    "api_key": "your-api-key",
    "timeout": 30
  },
  "ollama": {
    "base_url": "http://localhost:11434",
    "model": "llama3"
  }
}
```

---

## 🐛 故障排除

### 問題 1: 前端無法加載

**症狀**: 訪問 http://localhost:8000/ 顯示錯誤

**解決方案**:
1. 檢查 `templates/index.html` 是否存在
2. 檢查控制台錯誤日誌
3. 確認文件編碼為 UTF-8

### 問題 2: LLM 無法識別指令

**症狀**: 回覆「無法識別指令」

**解決方案**:
1. 檢查 `function_definitions_complete.json` 是否存在
2. 確認 EXCEL 解析成功
3. 查看日誌了解 LLM 回覆
4. 切換到 Mock 模式測試

### 問題 3: Mock API 無法運行

**症狀**: API 調用失敗

**解決方案**:
1. 檢查 `cmo_api_client.py` 是否正確導入
2. 確認 `use_mock=True`
3. 查看詳細錯誤日誌

### 問題 4: Ollama 連接失敗

**症狀**: 無法連接到 Ollama

**解決方案**:
```bash
# 1. 檢查 Ollama 是否運行
curl http://localhost:11434/api/tags

# 2. 檢查模型是否下載
ollama list

# 3. 手動啟動 Ollama
ollama serve

# 4. 如果失敗，使用 Mock 模式
use_ollama=False, use_mock=True
```

### 問題 5: 地圖無法顯示

**症狀**: 地圖區域空白

**解決方案**:
1. 檢查 `static/maps/` 目錄是否存在
2. 檢查地圖文件是否生成
3. 查看瀏覽器控制台錯誤
4. 確認 Leaflet.js CDN 可訪問

---

## 📊 API 文檔

### 端點列表

| 端點 | 方法 | 功能 |
|------|------|------|
| `/` | GET | 前端界面 |
| `/api/import_scenario` | POST | 兵棋場景匯入 |
| `/api/start_scenario` | POST | 啟動模擬 |
| `/api/get_wta` | POST | 武器分派 |
| `/api/get_answer` | POST | RAG 問答 |
| `/api/check_simulation_status/{id}` | GET | 查詢模擬狀態 |
| `/health` | GET | 健康檢查 |
| `/docs` | GET | API 文檔（Swagger） |

### 請求格式

所有 API 端點接受相同的請求格式：

```json
{
  "user_input": "使用者輸入訊息",
  "mode": "import_scenario",  // 或 start_scenario, get_wta, get_answer
  "system_prompt": "系統提示詞（可選）",
  "model": "llama3",          // 用於 get_answer
  "stream": false             // 用於 get_answer
}
```

### 回應格式

```json
{
  "success": true,
  "answer": "網頁顯示訊息",
  "map_url": "/static/maps/xxx.html",  // 可選
  "wta_data": {...},                    // 可選（get_wta）
  "simulation_id": "sim_xxx",           // 可選（start_scenario）
  "error": null
}
```

---

## 🧪 測試腳本

### 基礎測試

```bash
# 健康檢查
curl http://localhost:8000/health

# 測試 import_scenario
curl -X POST http://localhost:8000/api/import_scenario \
  -H "Content-Type: application/json" \
  -d '{
    "user_input": "繪製解放軍054A和055的座標",
    "mode": "import_scenario"
  }'
```

### Python 測試腳本

創建 `test_system.py`：

```python
import asyncio
import httpx

async def test_all_apis():
    client = httpx.AsyncClient()
    base_url = "http://localhost:8000"
    
    # 測試 1: import_scenario
    print("\n[測試 1] import_scenario")
    response = await client.post(f"{base_url}/api/import_scenario", json={
        "user_input": "繪製解放軍054A和055的座標",
        "mode": "import_scenario"
    })
    print(f"結果: {response.json()}")
    
    # 測試 2: start_scenario
    print("\n[測試 2] start_scenario")
    response = await client.post(f"{base_url}/api/start_scenario", json={
        "user_input": "開始模擬",
        "mode": "start_scenario"
    })
    print(f"結果: {response.json()}")
    
    # 測試 3: get_wta
    print("\n[測試 3] get_wta")
    response = await client.post(f"{base_url}/api/get_wta", json={
        "user_input": "如何攻擊052D",
        "mode": "get_wta"
    })
    print(f"結果: {response.json()}")
    
    await client.aclose()

asyncio.run(test_all_apis())
```

運行：
```bash
python test_system.py
```

---

## 📈 性能優化

### 生產環境建議

1. **使用 Gunicorn + Uvicorn**
   ```bash
   gunicorn cmo_backend_complete:app \
     --workers 4 \
     --worker-class uvicorn.workers.UvicornWorker \
     --bind 0.0.0.0:8000
   ```

2. **啟用快取**
   - 快取 Function Definitions
   - 快取地圖文件

3. **添加日誌**
   - 使用 Loguru 或 structlog
   - 設定日誌輪轉

4. **監控**
   - 添加 Prometheus metrics
   - 使用 Grafana 可視化

---

## 🔒 安全建議

1. **API Key 管理**
   - 不要硬編碼 API Key
   - 使用環境變數或配置文件
   - 定期輪換 Key

2. **CORS 設定**
   - 生產環境限制允許的來源
   - 不要使用 `allow_origins=["*"]`

3. **輸入驗證**
   - 已實現基本驗證
   - 考慮添加更嚴格的檢查

4. **速率限制**
   - 添加請求速率限制
   - 使用 slowapi 或 fastapi-limiter

---

## 📞 技術支援

### 問題排查順序

1. **查看日誌**: 所有操作都有詳細日誌
2. **健康檢查**: 訪問 `/health` 端點
3. **API 文檔**: 訪問 `/docs` 查看 Swagger 文檔
4. **測試腳本**: 運行測試腳本驗證功能

### 聯繫方式

- **中科院 API 問題**: 參考 `中科院API對接文檔.md`
- **系統問題**: 查看本文檔故障排除章節
- **Ollama 問題**: https://github.com/ollama/ollama/issues

---

## ✅ 部署檢查清單

部署前確認：

- [ ] Python 依賴已安裝
- [ ] EXCEL 文件已解析
- [ ] Function Definitions 已生成
- [ ] 前端文件在 templates/ 目錄
- [ ] Static 目錄已創建
- [ ] Ollama 已安裝並運行（如使用）
- [ ] 中科院 API 配置完成（如使用真實 API）
- [ ] 所有功能已測試
- [ ] 日誌正常輸出
- [ ] 健康檢查通過

---

## 🎉 總結



✅ **完整的 EXCEL 解析器** - 自動解析 4 個 API 規格  
✅ **智能的 LLM 客戶端** - Ollama + Llama3-8b  
✅ **完整的 API 客戶端** - Mock 和真實模式  
✅ **專業的 FastAPI 後端** - 支援 4 個功能  
✅ **美觀的前端界面** - 你提供的 UI  
✅ **完整的文檔** - 從安裝到部署  

**立即開始**:

```bash
# 1. 安裝依賴
pip install -r requirements.txt

# 2. 解析 EXCEL
python excel_parser.py

# 3. 放置前端
cp your-index.html templates/

# 4. 啟動系統
python cmo_backend_complete.py

# 5. 訪問
# http://localhost:8000/
```

---

**版本**: 1.0.0  
**日期**: 2025-12-23  

