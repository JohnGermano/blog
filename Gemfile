source "https://rubygems.org"

gem "jekyll", "~> 4.3.1"

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]

group :jekyll_plugins do
  gem 'jekyll-paginate'
  gem 'jekyll-tagsgenerator'
  gem 'jekyll-seo-tag'
  gem 'jekyll-sitemap'
end

# Lock `http_parser.rb` gem to `v0.6.x` on JRuby builds since newer versions of the gem
# do not have a Java counterpart.
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]
