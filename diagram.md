# G01 Website: Technical Architecture & Deployment Flow

This document describes the infrastructure architecture, automated deployment process, and Domain Name System (DNS) configuration for the G01 Website.

## 1. Infrastructure & Deployment Architecture
Describes the end-to-end path from local development to Vercel hosting and custom domain mapping.

```mermaid
graph TD
    subgraph Development_Env [Development Environment]
        Dev[Local Machine] -->|Git Push| GH[GitHub Repository]
    end

    subgraph CI_CD_Hosting [Hosting & Deployment - Vercel]
        GH -->|Webhook Trigger| V_Build[Vercel CI/CD Build]
        V_Build -->|Deployment Success| V_Edge[Vercel Edge Network / CDN]
    end

    subgraph Domain_Layer [Domain & DNS Configuration]
        DNS[DNS Provider / e.g. GoDaddy, Cloudflare]
        CNAME[www CNAME Record]
        A_Record[Apex @ A Record]
        
        DNS -->|Points to| CNAME
        DNS -->|Points to| A_Record
        CNAME -->|cname.vercel-dns.com| V_Edge
        A_Record -->|76.76.21.21| V_Edge
    end

    subgraph Client_Access [Client Side]
        User[Visitor Browser] -->|Enter g01.com.tw| DNS
        User -->|Fetch Assets| V_Edge
    end

    style V_Edge fill:#f9f,stroke:#333,stroke-width:2px
    style GH fill:#eee,stroke:#333
    style DNS fill:#fff,stroke:#004a99,stroke-width:2px
```

## 2. Domain Migration & Configuration Guide

Follow these steps to point your custom domain from your provider to Vercel:

### Step 1: Add Domain in Vercel
1. Navigate to **Vercel Dashboard** -> Select your Project.
2. Go to **Settings** -> **Domains**.
3. Enter your domain (e.g., `www.g01.com.tw`) and click **Add**.

### Step 2: Configure DNS Records
Log in to your domain registrar (e.g., GoDaddy, Namecheap) and add the following records:

| Type | Name | Value / Alias | Description |
| :--- | :--- | :--- | :--- |
| **CNAME** | `www` | `cname.vercel-dns.com` | Maps the 'www' subdomain to Vercel |
| **A** | `@` | `76.76.21.21` | Maps the Apex domain (root) to Vercel IP |

### Step 3: DNS Propagation
- It usually takes 10 minutes to several hours for the DNS changes to propagate globally.
- Vercel will automatically provision and renew your **SSL Certificate (HTTPS)** once the records are verified.

## 3. User Flow Diagram

```mermaid
flowchart TD
    Start((Visitor Enters URL)) --> DNS{DNS Query}
    DNS -->|www / root| Vercel[Vercel Edge Network]
    Vercel --> SSL[SSL/TLS Validation - HTTPS]
    SSL --> Index[Load index.html]
    
    Index --> Nav{Navigation}
    Nav --> About[About G01 - about.html]
    Nav --> Products[Products & Lines - products.html]
    
    subgraph Dynamic_Rendering [Dynamic Page Rendering]
        JS[Vanilla JS / Intersection Observer]
        CSS[CSS3 Reveal Animations]
    end
    
    About --- Dynamic_Rendering
    Products --- Dynamic_Rendering
    Index --- Dynamic_Rendering
```

## 4. Technical Specifications Summary
- **Source Control**: GitHub (Private/Public Repository).
- **Deployment Strategy**: Vercel CI/CD (Automated builds triggered by Git Push).
- **Domain Mapping**: CNAME (www) + A Record (@) targeting Vercel Anycast IP.
- **Performance & Security**: Vercel Global Edge Network (CDN) with automated SSL via Let's Encrypt.
