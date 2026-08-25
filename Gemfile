source "https://rubygems.org"

# 使用 GitHub Pages 官方锁定的 gem 组合，保证本地渲染和线上 GitHub Pages 构建完全一致
gem "github-pages", group: :jekyll_plugins

# Windows 下 jekyll 需要这个来正确处理时区
gem "tzinfo-data", platforms: [:mingw, :mswin, :x64_mingw, :jruby]

# 注：wdm（Windows 原生文件监听）在新版 Ruby 下编译不过，故不引入；
# 本地跑 `jekyll serve` 时如果改了文件页面没自动刷新，加 --force_polling 参数即可
