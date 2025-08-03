source "https://rubygems.org"

gem "jekyll", "~> 4.3.0"

# SASS 컴파일러 변경 (호환성 문제 해결)
gem "sassc", "~> 2.0"
gem "jekyll-sass-converter", "~> 2.0"

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.12"
  gem "jekyll-sitemap"
  gem "jekyll-seo-tag"
end

platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]