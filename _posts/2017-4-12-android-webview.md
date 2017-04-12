---
layout: post
title:  "Android webview"
date:   2017-04-12 22:20:05
categories: webview
excerpt: 
---

* content
{:toc}


### Android webview相关

---

#### 销毁webview：

        if (webView != null) {
            webView.stopLoading();
            webView.setWebViewListener(null);
            webView.clearHistory();
            webView.clearCache(true);
            webView.loadUrl("about:blank");
            webView.pauseTimers();
            webView = null;
        }

#### 清除历史记录

        @Override
        public void doUpdateVisitedHistory(WebView webView, String s, boolean b) {
            if (needClearHistory) {
                webView.clearHistory();
                needClearHistory = false;
            }
            
        }

---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---