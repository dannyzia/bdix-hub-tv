# BDIX Hub TV 📺⚡

A lightning-fast, 10-foot remote-optimized TV launcher and web hub designed specifically for Android TV boxes (such as the **Mi TV Box 4 / 4K**). It aggregates **106 BDIX media servers, FTP portals, and live TV streams** (285 total mirror links) into a single, sleek, offline-safe dashboard.

![BDIX Hub TV Preview](https://img.shields.io/badge/Android%20TV-Leanback%20Ready-orange?style=for-the-badge&logo=android)
![Vercel Deployment](https://img.shields.io/badge/Vercel-Auto%20Deploy-black?style=for-the-badge&logo=vercel)
![Zero Dependencies](https://img.shields.io/badge/Dependencies-Zero%20CDN-success?style=for-the-badge)

---

## ✨ Key Features

- **Favorites-First Startup:** Opens directly to your curated **Favorites** screen so your working servers are instantly available.
- **106 Providers & 285 Mirrors:** Pre-loaded with comprehensive BDIX media, FTP storage nodes, Jellyfin/Emby streams, and Live TV/IPTV portals.
- **Live BDIX Reachability Scanner (`Scan & Sort`):** Proves and pings servers in parallel batches of 6, automatically sorting reachable (`ONLINE`) servers to the top of your grid.
- **2D Spatial D-Ped Engine:** Custom geometric focus routing designed specifically for TV remotes, ensuring smooth navigation between the left sidebar and the main content grid.
- **Hold-OK Mirror & Favorites Modal:** Long-press **OK** (`520ms`) on any card to inspect all alternative IPs/mirrors, test their status, and set a custom default server.
- **Star Mode (`Edit ★`):** Toggle Star Mode on to instantly favorite or unfavorite servers with a single click of the remote's **OK** button.
- **Zero External Dependencies:** Built with inline CSS and an SVG icon sprite. It loads instantly and functions seamlessly even when international internet is down (BDIX-only local routing).
- **Custom BDIX URL Launcher:** Directly type and browse any custom IP or domain (e.g., `172.16.50.4:8096`) right from your TV remote.

---

## 🚀 Quick Deploy to Vercel

Hosting this on Vercel allows you to update server IPs or add new providers instantly without rebuilding or reinstalling your Android TV APK:

1. Fork or clone this repository.
2. Ensure the main launcher file is named `index.html`.
3. Import the project into [Vercel](https://vercel.com):
   - **Framework Preset:** `Other`
   - **Build Command:** *(Leave blank)*
   - **Output Directory:** `.` (root)
4. Click **Deploy**. Your BDIX TV Hub is now live on a secure HTTPS URL!

---

## 📦 Converting to an Android TV App (APK)

To make this web hub appear as a native app right on your Mi TV Box home screen:

### Option A: The Quick Way (Browser Bookmark)
1. Install **TV Bro** or **Puffin TV Browser** from the Google Play Store on your Android TV.
2. Open your Vercel deployment URL (or local network IP).
3. Set it as your browser homepage.

### Option B: Build a Dedicated Android TV APK (Android Studio)
Create an Android Studio project with a `MainActivity.java` that wraps the hub in a WebView and intercepts video file downloads to hand them off to **VLC Player**:

```java
package com.bdix.tvhub;

import android.app.Activity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.webkit.WebResourceRequest;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.webkit.WebViewClient;

public class MainActivity extends Activity {
    private WebView webView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        webView = new WebView(this);
        setContentView(webView);

        WebSettings settings = webView.getSettings();
        settings.setJavaScriptEnabled(true);
        settings.setDomStorageEnabled(true);
        settings.setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
        
        // Crucial for Mi Box D-pad navigation
        webView.setFocusable(true);
        webView.setFocusableInTouchMode(true);

        // Intercept video file links to launch VLC
        webView.setWebViewClient(new WebViewClient() {
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
                String url = request.getUrl().toString().toLowerCase();
                if (url.endsWith(".mkv") || url.endsWith(".mp4") || url.endsWith(".ts") || url.endsWith(".m3u8")) {
                    try {
                        Intent intent = new Intent(Intent.ACTION_VIEW);
                        intent.setPackage("org.videolan.vlc");
                        intent.setDataAndType(Uri.parse(url), "video/*");
                        startActivity(intent);
                        return true;
                    } catch (Exception e) {
                        // Fallback if VLC isn't installed
                    }
                }
                return false; // Browse FTP directory normally inside WebView
            }
        });

        // Load your Vercel URL (or fallback to file:///android_asset/index.html)
        webView.loadUrl("https://your-vercel-deployment.vercel.app");
    }
}
```

Make sure to add `android:usesCleartextTraffic="true"` inside your `<application>` tag in `AndroidManifest.xml` to allow insecure `http://` BDIX connections.

---

## 🎮 Remote Control Shortcuts

| Remote Key | Action |
| :--- | :--- |
| **D-Pad (▲ ▼ ◀ ▶)** | Move focus across sidebar, search bar, and grid cards |
| **OK / Select** | Open server portal / Select option |
| **Hold OK (`0.5s`)** | Open Mirrors & Details modal |
| **`S` or `/`** | Jump to search bar |
| **`F`** | Quick-toggle favorite on focused server |
| **`1` – `6`** | Jump directly to category tabs (Favorites, All, Media, TV, IP, Recent) |
| **Back Button** | Return to top / Exit search / Close modal |

---

## 📄 License
MIT License. Feel free to modify and share for personal and community use!