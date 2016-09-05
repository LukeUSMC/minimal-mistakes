require "rubygems"
require "tmpdir"
require "bundler/setup"
require "jekyll"

GITHUB_REPONAME = "LukeUSMC/lukeusmc.github.io"


namespace :site do
  desc "Generate blog files"
  task :generate do
    Jekyll::Site.new(Jekyll.configuration({
      "source"      => ".",
      "destination" => "_site"
    })).process
  end

  desc "Serve Dev"
  task :serve_dev do
    system "bundle exec jekyll serve --config _config.yml,_config_dev.yml"
  end



  desc "Generate and publish blog to gh-pages"
  task :publish => [:generate] do
    Dir.mktmpdir do |tmp|
      cp_r "_site/.", tmp
      Dir.chdir tmp
      system "git init"
      system "git add ."
      message = "Site updated at #{Time.now.utc}"
      system "git commit -m #{message.inspect}"
      system "git remote add origin git@github.com:#{GITHUB_REPONAME}.git"
      system "git push origin master --force"
    end
  end
end
