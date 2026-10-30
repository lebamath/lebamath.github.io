---
layout: default
title: 首页 · Home
ad_lang: en
---

<div class="index-toolbar">
  <div class="lang-switch" role="group" aria-label="切换语言 / Language">
    <button type="button" class="lang-switch__btn" data-lang="zh">中文</button>
    <button type="button" class="lang-switch__btn is-active" data-lang="en">English</button>
  </div>
  <div class="fold-all" role="group" aria-label="展开/折叠 Expand/Collapse">
    <button type="button" class="fold-all__btn" data-action="expand">Expand all</button>
    <button type="button" class="fold-all__btn" data-action="collapse">Collapse all</button>
  </div>
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

  // ---- 标题折叠 ----------------------------------------------------------
  // 抓回来的内容里标题和后面的链接段落是平铺的同级元素，没有"节"的外壳。
  // 这里在运行时把每个 h1/h2/h3 连同它后面的内容（直到下一个同级或更高级
  // 标题）包进一个 <section class="fold">，低一级的标题嵌套在高一级的节里。
  // 不改动 chinese_version.md / english_version.md。
  // 折叠状态按语言存进 localStorage：{ zh: {标题id: true}, en: {...} }，
  // 记的是"被折叠的"标题，所以没记录的（包括新加的标题）默认都是展开的。
  var FOLD_KEY = 'index-folds-v1';
  var folds = {};
  try { folds = JSON.parse(localStorage.getItem(FOLD_KEY)) || {}; } catch (e) { folds = {}; }
  var currentLang = 'en';
  var hashHandled = false;

  function saveFolds() {
    try { localStorage.setItem(FOLD_KEY, JSON.stringify(folds)); } catch (e) {}
  }

  function setCollapsed(section, collapsed) {
    var head = section.firstElementChild;
    var map = folds[currentLang] || (folds[currentLang] = {});
    section.classList.toggle('is-collapsed', collapsed);
    head.querySelector('.fold__toggle').setAttribute('aria-expanded', String(!collapsed));
    if (collapsed) { map[head.id] = true; } else { delete map[head.id]; }
  }

  function buildFolds(root, lang) {
    currentLang = lang;
    var out = document.createDocumentFragment();
    var stack = [];
    Array.prototype.slice.call(root.children).forEach(function (node) {
      var m = /^H([1-3])$/.exec(node.tagName);
      if (!m) {
        (stack.length ? stack[stack.length - 1].body : out).appendChild(node);
        return;
      }
      var level = +m[1];
      while (stack.length && stack[stack.length - 1].level >= level) stack.pop();

      var section = document.createElement('section');
      section.className = 'fold';
      var body = document.createElement('div');
      body.className = 'fold__body';

      // 按钮里没有文字（箭头由 CSS 画），不会影响目录用 h.textContent 取标题文字
      var toggle = document.createElement('button');
      toggle.type = 'button';
      toggle.className = 'fold__toggle';
      toggle.setAttribute('aria-expanded', 'true');
      toggle.setAttribute('aria-label', node.textContent);
      node.classList.add('fold__head');
      node.insertBefore(toggle, node.firstChild);

      section.appendChild(node);
      section.appendChild(body);
      (stack.length ? stack[stack.length - 1].body : out).appendChild(section);
      stack.push({ level: level, body: body });
    });
    root.appendChild(out);

    var map = folds[lang] || {};
    Array.prototype.forEach.call(root.querySelectorAll('.fold'), function (section) {
      if (!section.lastElementChild.firstElementChild) {
        section.classList.add('is-empty');
        return;
      }
      setCollapsed(section, !!map[section.firstElementChild.id]);
    });
  }

  // 展开某个标题所在的节以及它所有的上级节（目录跳转、URL 带 #锚点时用）
  function revealHeading(id) {
    var el = document.getElementById(id);
    if (!el || !container.contains(el)) return null;
    var changed = false;
    var section = el.closest('.fold');
    while (section) {
      if (section.classList.contains('is-collapsed')) {
        setCollapsed(section, false);
        changed = true;
      }
      section = section.parentNode.closest('.fold');
    }
    if (changed) saveFolds();
    return el;
  }

  // 点标题（含箭头按钮）折叠/展开
  container.addEventListener('click', function (e) {
    var head = e.target.closest('.fold__head');
    if (!head || e.target.closest('a')) return;
    var section = head.parentNode;
    if (section.classList.contains('is-empty')) return;
    setCollapsed(section, !section.classList.contains('is-collapsed'));
    saveFolds();
  });

  // 点目录项：目标标题可能在折叠的节里，要在目录自己的点击处理（滚动）之前
  // 先展开，所以用捕获阶段——scrollIntoView 对被隐藏的元素不起作用。
  container.addEventListener('click', function (e) {
    var link = e.target.closest('.article-toc__link');
    if (link) revealHeading(link.getAttribute('href').slice(1));
  }, true);

  // 全部展开 / 全部折叠
  Array.prototype.forEach.call(document.querySelectorAll('.fold-all__btn'), function (btn) {
    btn.addEventListener('click', function () {
      var collapse = btn.getAttribute('data-action') === 'collapse';
      Array.prototype.forEach.call(container.querySelectorAll('.fold:not(.is-empty)'), function (section) {
        setCollapsed(section, collapse);
      });
      saveFolds();
    });
  });

  var foldAllLabels = {
    zh: { expand: '全部展开', collapse: '全部折叠' },
    en: { expand: 'Expand all', collapse: 'Collapse all' }
  };

  // 内容是异步塞进来的，浏览器自己的 #锚点定位早在内容到达之前就跑完了（找不到
  // 目标），所以页面第一次渲染完之后手动处理一次：展开目标所在的节并滚过去。
  function handleInitialHash() {
    if (!location.hash) return;
    var id;
    try { id = decodeURIComponent(location.hash.slice(1)); } catch (e) { return; }
    var el = revealHeading(id);
    if (el) el.scrollIntoView();
  }

  // 每次把内容塞进页面之后都要做的事。先建折叠结构再生成目录（目录扫描的是
  // 标题元素，嵌在 section 里也扫得到）。
  // 目录是运行时扫描标题生成的，不在抓回来的静态 HTML 里，要重新生成（见
  // _layouts/default.html）。首页每个 # 都是独立一节，没有单独的"文章标题"，所以
  // 用 placement: 'before' 把目录放在切换按钮之后、第一节标题之前。
  // 目录标题（"目录" / "On this page"）要跟着当前切换的语言走，不能用 page.lang——
  // 这个静态页面本身没有固定语言，所以显式传 lang 覆盖。
  function afterRender(lang) {
    buildFolds(container, lang);
    if (window.buildArticleToc) window.buildArticleToc(container, { placement: 'before', lang: lang });
    retypesetMath(container);
    if (!hashHandled) {
      hashHandled = true;
      handleInitialHash();
    }
  }

  function show(lang) {
    buttons.forEach(function (b) {
      b.classList.toggle('is-active', b.getAttribute('data-lang') === lang);
    });
    Array.prototype.forEach.call(document.querySelectorAll('.fold-all__btn'), function (btn) {
      btn.textContent = foldAllLabels[lang][btn.getAttribute('data-action')];
    });

    if (cache[lang]) {
      container.innerHTML = cache[lang];
      afterRender(lang);
      return;
    }

    fetch(sources[lang])
      .then(function (res) { return res.text(); })
      .then(function (html) {
        var doc = new DOMParser().parseFromString(html, 'text/html');
        var content = doc.querySelector('.content-col');
        cache[lang] = content ? content.innerHTML : '';
        container.innerHTML = cache[lang];
        afterRender(lang);
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
