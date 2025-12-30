# TAICA_project - AI 異國菜單翻譯與美食具象化助手
> **拍菜單 -> 秒翻譯 -> 看美食** | 基於 Multimodal AI 的 0 成本解決方案

## Introduction

出國旅遊時，面對寫滿法文或德文的純文字菜單，你是否也遇過這種「雙重困境」？
1.  **語言隔閡**：看不懂文字，Google 翻譯後還是不知道是什麼。
2.  **視覺落差**：無法想像這道菜的份量、擺盤或食材，點餐像在賭博。

**美食具象化助手** 是一個結合 **OCR 文字辨識**、**語意翻譯** 與 **生成式 AI 繪圖** 的網頁應用。它能將看不懂的菜單瞬間轉化為「詳細的中文介紹」加上「擬真的美食預覽圖」，輔助旅客快速做出點餐決策。


## Demo
> [3 分鐘介紹影片](https://youtu.be/ed201nbGWG4)
> [程式碼](期末專案.ipynb) 

###  核心功能
*  智能辨識：支援上傳 JPG/PNG 菜單圖片。
*  語意分析：精準提取菜名、價格，並生成繁體中文口感介紹。
*  風格化生圖：自動生成2.5D、高飽和度、微動漫感的美食預覽圖。
*  極速體驗：採用混合雲架構，平均 3-5 秒完成分析與生圖。

## 技術架構 Tech Stack

採用「混合模型策略」，解決了依賴付費模型 (GPT-4o/DALL-E 3) 成本過高的問題，實現了0 成本的友善方案。

| Component | Technology | 選擇原因 (Why?) |
| :--- | :--- | :--- |
| **Frontend UI** | **Streamlit** | 快速構建互動式網頁，支援即時渲染。 |
| **Vision & Logic** | **Google Gemini 2.5 Flash** | 擁有極強的視覺理解能力與 OCR 速度，且 API 免費額度充足。 |
| **Image Gen** | **Pollinations.ai (Turbo)** | 基於 SDXL Turbo，生成速度極快 (1-2s)，取代昂貴的 DALL-E 3。 |
| **Deployment** | **Cloudflare Tunnel** | 解決 Google Colab 無法直接對外提供網頁服務的問題。 |

### 關鍵技術：後端風格注入
為了彌補免費生圖模型畫質不穩定的問題，我們在後端實作了自動化的 Prompt Engineering 機制。

```python
# 核心程式碼片段
def generate_image_url(prompt):
    # Gemini 只負責寫內容，風格由程式碼強制注入
    style_suffix = ", (Nano Banana style:1.2), 2.5D, vibrant colors, soft lighting..."
    final_prompt = clean_prompt + style_suffix
    
    # 呼叫 Turbo 模型進行渲染
    return call_pollinations_api(final_prompt)
