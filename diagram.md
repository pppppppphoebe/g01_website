# 機零壹科技網站：技術架構與部署流程

本文件描述機零壹科技網站的基礎設施架構、自動化部署流程以及網域 (DNS) 設定。

## 1. 基礎設施與部署架構 (Infrastructure & Deployment)
描述從開發到網域指向 Vercel 的完整路徑。

```mermaid
graph TD
    subgraph Development [開發環境]
        Dev[開發者本地端] -->|Git Push| GH[GitHub Repository]
    end

    subgraph CI_CD_Hosting [託管與部署 - Vercel]
        GH -->|Webhooks 觸發| V_Build[Vercel 自動建置]
        V_Build -->|部署成功| V_Edge[Vercel Edge Network / CDN]
    end

    subgraph Domain_Layer [網域與 DNS 設定]
        DNS[DNS 服務商 / 如 GoDaddy, Cloudflare]
        CNAME[www CNAME 紀錄]
        A_Record[Apex @ A 紀錄]
        
        DNS -->|指向| CNAME
        DNS -->|指向| A_Record
        CNAME -->|cname.vercel-dns.com| V_Edge
        A_Record -->|76.76.21.21| V_Edge
    end

    subgraph Client_Access [使用者端]
        User[訪客瀏覽器] -->|輸入 g01.com.tw| DNS
        User -->|存取內容| V_Edge
    end

    style V_Edge fill:#f9f,stroke:#333,stroke-width:2px
    style GH fill:#eee,stroke:#333
    style DNS fill:#fff,stroke:#004a99,stroke-width:2px
```

## 2. 網域遷移與設定步驟 (Domain Migration Guide)

如果您要將網域從其他地方遷過來並指向 Vercel，請依照以下步驟設定：

### 第一步：在 Vercel 專案中新增網域
1. 進入 Vercel Dashboard -> 選擇您的專案。
2. 點擊 **Settings** -> **Domains**。
3. 輸入您的網域 (例如 `www.g01.com.tw`) 並點擊 Add。

### 第二步：前往 DNS 服務商進行設定
登入您的網域註冊商 (如 GoDaddy, Hinet, Cloudflare)，找到 DNS 管理介面並新增以下紀錄：

| 類型 (Type) | 主機紀錄 (Name) | 指向地址 (Value / Alias) | 說明 |
| :--- | :--- | :--- | :--- |
| **CNAME** | `www` | `cname.vercel-dns.com` | 將子網域 www 指向 Vercel |
| **A** | `@` | `76.76.21.21` | 將主網域 (Apex) 指向 Vercel IP |

### 第三步：等待生效 (DNS Propagation)
- 修改後大約需要 10 分鐘到幾小時不等的時間生效。
- Vercel 會自動為您申請並更新 **SSL 憑證 (HTTPS)**。

## 3. 使用者瀏覽流程 (User Flow)

```mermaid
flowchart TD
    Start((訪客輸入網址)) --> DNS{DNS 查詢}
    DNS -->|www| Vercel[Vercel Edge Network]
    Vercel --> SSL[SSL 憑證校驗 - HTTPS]
    SSL --> Index[載入 index.html]
    
    Index --> Nav{導覽切換}
    Nav --> About[關於機零壹 about.html]
    Nav --> Products[產品與生產線 products.html]
    
    subgraph Page_Effects [頁面動態渲染]
        JS[Vanilla JS / Intersection Observer]
        CSS[CSS3 Animations]
    end
    
    About --- Page_Effects
    Products --- Page_Effects
    Index --- Page_Effects
```

## 4. 技術規格摘要
- **原始碼託管**：GitHub (Private/Public Repo)。
- **自動化部署**：Vercel CI/CD (偵測 Git Push 自動更新)。
- **網域指向**：CNAME (www) + A Record (@)。
- **加速與安全**：Vercel Global Edge Network + 自動 HTTPS (Let's Encrypt)。
