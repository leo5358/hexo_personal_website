---
title: 關於我
date: 2026-02-27 22:40:00
type: "about"
comments: false
---

## 👋 關於我 (About Me)

empty, 等我晚點寫

<br><hr><br>

## 📊 數據統計 (Stats)

{% raw %}
<div style="display: flex; flex-direction: column; gap: 40px; margin-top: 20px;">
<div>
<h3 style="margin-bottom: 15px; border-bottom: none; text-align: left; color: #ffffff; font-size: 1.3em; font-weight: 600;">🟢 LeetCode 戰績</h3>
<div style="text-align: center;">
<img src="https://leetcard.jacoblin.cool/Leo_from_tw_237?theme=dark&font=Noto%20Sans%20TC&ext=contest" alt="LeetCode Full Stats" style="max-width: 100%; border-radius: 8px;">
</div>
</div>
<hr style="border: 0; border-top: 1px solid #333; margin: 0;">
<div style="display: flex; flex-direction: column; gap: 30px;">
<div>
<h3 style="margin-bottom: 15px; border-bottom: none; text-align: left; color: #ffffff; font-size: 1.3em; font-weight: 600;">🐙 GitHub 貢獻度</h3>
<div style="text-align: center; background: #242424; padding: 15px; border-radius: 8px; border: 1px solid #333; min-height: 120px; display: flex; align-items: center; justify-content: center;">
<img src="https://ghchart.rshah.org/eb6b56/leo5358" alt="GitHub Contributions Graph" style="max-width: 100%;">
</div>
</div>
<div>
<h3 style="margin-bottom: 15px; border-bottom: none; text-align: left; color: #ffffff; font-size: 1.3em; font-weight: 600;">📁 精選專案與常用語言</h3>
<div id="github-repos-container" style="display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 15px;"></div>
<script>
const myRepos = ["spotlight-like-window-", "latex_calc", "tree"];
const vercelUrl = "https://github-readme-stats-iota-eight-83.vercel.app";
const username = "leo5358";
const container = document.getElementById("github-repos-container");
let htmlContent = "";

// 🚨 終極魔法設定：外層容器的統一 CSS (延展高度 + 垂直置中 + 模擬卡片外觀)
const aStyle = "display: flex; align-items: center; justify-content: center; background: #242424; border: 1px solid #333; border-radius: 8px; text-decoration: none; height: 100%; box-sizing: border-box; overflow: hidden;";

myRepos.forEach(repo => {
  // 注意這裡加入了 &hide_border=true
  const imgUrl = `${vercelUrl}/api/pin/?username=${username}&repo=${repo}&bg_color=242424&title_color=eb6b56&text_color=e0e0e0&icon_color=eb6b56&hide_border=true`;
  htmlContent += `<a href="https://github.com/${username}/${repo}" target="_blank" style="${aStyle}"><img src="${imgUrl}" alt="${repo}" style="width: 100%; height: auto; display: block;"></a>`;
});

// 注意這裡加入了 &hide_border=true
const langUrl = `${vercelUrl}/api/top-langs/?username=${username}&layout=compact&bg_color=242424&title_color=eb6b56&text_color=e0e0e0&hide_border=true`;
htmlContent += `<a href="https://github.com/${username}" target="_blank" style="${aStyle}"><img src="${langUrl}" alt="Most Used Languages" style="width: 100%; height: auto; display: block;"></a>`;

container.innerHTML = htmlContent;
</script>
</div>
</div>
</div>
{% endraw %}