# 機零壹科技網站架構與流程圖

本文件使用 Mermaid 語法描述機零壹科技 (G01 Website) 的系統架構與使用者操作流程。

## 1. 系統架構圖 (System Architecture)
描述網站的組成元件與技術層次。

```mermaid
graph TD
    subgraph Client_Layer [用戶端 - 瀏覽器]
        UI[HTML5 / CSS3 / Vanilla JS]
        ScrollAnim[Intersection Observer API - 捲動動畫]
        Slider[JS Image Slider - 自動輪播]
    end

    subgraph Static_Assets [靜態資源層]
        Style[assets/style.css - 工業風樣式]
        Images[assets/images/ - 產品與工廠圖片]
        Fonts[Source Sans 3 / 思源黑體]
    end

    subgraph Hosting_Layer [部署層]
        Server[靜態網頁伺服器 / GitHub Pages]
    end

    UI --> Style
    UI --> Images
    UI --> Fonts
    UI -.-> ScrollAnim
    UI -.-> Slider
    Server --- Client_Layer
```

## 2. 使用者流程圖 (User Flow)
描述訪客進入網站後的導覽行為與互動。

```mermaid
flowchart TD
    Start((訪客進入網站)) --> Home[首頁 index.html]
    
    Home -->|查看核心優勢| Advantage[核心優勢區塊]
    Home -->|查看導光板應用| Slider[自動圖片輪播]
    
    Home --> Nav{頂部導覽列}
    
    Nav -->|關於我們| About[關於機零壹 about.html]
    Nav -->|產品資訊| Products[產品與生產線 products.html]
    
    subgraph About_Page [關於頁面內容]
        About --> Intro[公司簡介]
        About --> Timeline[專業沿革時間軸]
    end
    
    subgraph Products_Page [產品頁面內容]
        Products --> Materials[加工材質說明]
        Products --> SpecSlider[技術規範輪播]
        Products --> Gallery[生產現場藝廊]
    end
    
    About_Page --> Footer[頁尾聯繫資訊]
    Products_Page --> Footer
    Home --> Footer
    
    Footer --> End((結束瀏覽/聯繫合作))
```

## 3. 技術規格摘要
- **前端技術**：HTML5, CSS3, Vanilla JavaScript (無外部 Framework 依賴)。
- **字體設計**：Source Sans 3 (英) + Noto Sans TC (中)。
- **動態效果**：
    - 使用 `Intersection Observer` 實現元素捲動浮現 (Reveal effect)。
    - 使用 `CSS Keyframes` 搭配 JS 控制圖片淡入淡出輪播。
- **響應式設計**：針對 Mobile 進行 Media Query 優化，修復背景與導航列排版。
