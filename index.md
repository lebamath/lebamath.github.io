---
layout: default
title: 首页 · Home
ad_lang: en
---

<div class="lang-switch" role="group" aria-label="切换语言 / Language">
  <button type="button" class="lang-switch__btn" data-lang="zh">中文</button>
  <button type="button" class="lang-switch__btn is-active" data-lang="en">English</button>
</div>

<div id="index-embed">
  <p class="index-embed__loading">Loading…</p>
</div>

<noscript>
  <p><a href="{{ '/chinese_version.html' | relative_url }}">中文版本</a> &middot; <a href="{{ '/english_version.html' | relative_url }}">English version</a></p>
</noscript>

<script>
(function () {
  var container = document.getElementById('index-embed');
  var buttons = document.querySelectorAll('.lang-switch__btn');
  var cache = {};

  // 直接借用 chinese_version.html / english_version.html 已经生成好的正文内容，
  // 不复制内容、不改动这两个页面本身——它们的链接和标题由你自己维护，
  // 这里只是运行时把渲染好的 .content-col 内容抓过来显示。
  var sources = {
    zh: '{{ "/chinese_version.html" | relative_url }}',
    en: '{{ "/english_version.html" | relative_url }}'
  };

  function show(lang) {
    buttons.forEach(function (b) {
      b.classList.toggle('is-active', b.getAttribute('data-lang') === lang);
    });

    if (cache[lang]) {
      container.innerHTML = cache[lang];
      return;
    }

    fetch(sources[lang])
      .then(function (res) { return res.text(); })
      .then(function (html) {
        var doc = new DOMParser().parseFromString(html, 'text/html');
        var content = doc.querySelector('.content-col');
        cache[lang] = content ? content.innerHTML : '';
        container.innerHTML = cache[lang];
      })
      .catch(function () {
        container.innerHTML = '<p><a href="' + sources[lang] + '">' +
          (lang === 'zh' ? '点击查看中文版' : 'View English version') + '</a></p>';
      });
  }

  buttons.forEach(function (btn) {
    btn.addEventListener('click', function () {
      show(btn.getAttribute('data-lang'));
    });
  });

  // 支持 index.html?lang=zh / ?lang=en：这样各篇文章的"返回主目录"链接
  // 可以带上语言参数，从中文文章点返回时首页直接显示中文，不用再手动点一次切换。
  var requested = new URLSearchParams(location.search).get('lang');
  show(requested === 'zh' ? 'zh' : 'en');
})();
</script>
