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

  // MathJax（v2）只在页面首次加载时自动扫描一次全文，之后用 innerHTML
  // 动态塞进来的公式它并不知道，需要手动让它重新扫描一次指定的节点。
  // 如果脚本这时候还没加载完（异步加载，首次 show() 调用很可能更早），
  // 就什么都不用做——它自己启动时的那一遍全文扫描会连带把这块内容扫到。
  function retypesetMath(node) {
    if (window.MathJax && window.MathJax.Hub) {
      window.MathJax.Hub.Queue(['Typeset', window.MathJax.Hub, node]);
    }
  }

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
      // 本页目录是运行时扫描标题生成的，不在抓回来的静态 HTML 里，
      // 每次把内容塞进页面之后都要重新生成一次（见 _layouts/default.html）。
      // 首页每个 # 都是独立一节，没有单独的"文章标题"，所以用 placement: 'before'
      // 把目录放在中文/English切换按钮之后、第一节标题之前（而不是普通文章那样
      // 放在文章标题后面）。
      // 目录标题（"目录" / "On this page"）要跟着当前切换的语言走，不能用
      // page.lang——这个静态页面本身没有固定语言，构建时 page.lang 不知道
      // 用户点的是哪个，所以显式传 lang 覆盖。
      if (window.buildArticleToc) window.buildArticleToc(container, { placement: 'before', lang: lang });
      retypesetMath(container);
      return;
    }

    fetch(sources[lang])
      .then(function (res) { return res.text(); })
      .then(function (html) {
        var doc = new DOMParser().parseFromString(html, 'text/html');
        var content = doc.querySelector('.content-col');
        cache[lang] = content ? content.innerHTML : '';
        container.innerHTML = cache[lang];
        if (window.buildArticleToc) window.buildArticleToc(container, { placement: 'before', lang: lang });
        retypesetMath(container);
      })
      .catch(function () {
        container.innerHTML = '<p><a href="' + sources[lang] + '">' +
          (lang === 'zh' ? '点击查看中文版' : 'View English version') + '</a></p>';
      });
  }

  buttons.forEach(function (btn) {
    btn.addEventListener('click', function () {
      var lang = btn.getAttribute('data-lang');
      show(lang);

      // 把选中的语言同步写回地址栏（不新增一条历史记录），
      // 这样刷新页面时能读到 ?lang=xxx，不会又跳回默认的英文。
      var url = new URL(location.href);
      url.searchParams.set('lang', lang);
      history.replaceState(null, '', url);
    });
  });

  // 支持 index.html?lang=zh / ?lang=en：这样各篇文章的"返回主目录"链接
  // 可以带上语言参数，从中文文章点返回时首页直接显示中文，不用再手动点一次切换。
  var requested = new URLSearchParams(location.search).get('lang');
  show(requested === 'zh' ? 'zh' : 'en');
})();
</script>
