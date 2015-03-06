# encoding: utf-8

require "json"

url_pow     = "arrrr2014"
url_live    = "arrrrcamp.be"

deploy_user = "arrrrcamp2014@pro-009.openminds.be"
deploy_path = "~/apps/arrrrcamp14/public"

desc "Delete old website files to start fresh."
task :clean do
  puts "Starting fresh!"
  system "rm -rf public"
end

desc "Design, write, and edit live."
task :default => [:clean] do
  pids = [
    spawn("jekyll serve"),
    spawn("sass --watch _source/_assets/sass:_source/assets/stylesheets"),
    spawn("coffee --bare --watch --join _source/assets/javascript/scripts.js --compile _source/_assets/coffeescript/*.coffee")
  ]

  trap "INT" do
    Process.kill "INT", *pids
    exit 1
  end

  loop do
    sleep 1
  end
end

desc "Creates a Pow link. http://pow.cx"
task :pow do
  system "ln -s '#{__dir__}' ~/.pow/#{url_pow}"
  puts "Set up a Pow site at http://#{url_pow}.dev."
end

desc "Generate a copy of the most current site."
task :compile do
  system "jekyll build"
  system "sass --style compressed _source/_assets/sass/style.scss:public/assets/stylesheets/style.css"
  system "coffee --bare --join public/assets/javascript/scripts.js --compile _source/_assets/coffeescript/*.coffee"
end

desc "Compress JavaScript."
task :compress do
  system "uglifyjs public/assets/javascript/scripts.js --compress --output public/assets/javascript/scripts.js"
  puts "Compressed JavaScript."
end

desc "Build a clean, compressed copy of the site."
task :build => [:clean, :compile, :compress] do
  puts "Done! See it locally at http://#{url_pow}.dev, or live at http://#{url_live}."
end

desc "Upload a fresh copy of the site to your server."
task :deploy => [:build] do
  puts "Deploying at http://#{url_live}!"
  system "rsync -avze 'ssh -p 22' --delete public/ #{deploy_user}:#{deploy_path}"
end

desc "Builds a fresh copy of your site, then opens it."
task :view => [:build] do
  system "open http://#{url_pow}.dev"
end

namespace :view do
  desc "View your site live."
  task :live do
    system "open http://#{url_live}"
  end

  desc "Generate an xip.io friendly URL."
  task :xip do
    ip = `ifconfig | grep 'inet ' | grep -v '127.0.0.1' | awk '{print $2}' | awk '{ printf "%s", $0 }'`
    url = "http://#{url_pow}.#{ip}.xip.io/"
    puts "Opening #{url}…"
    system "open #{url}"
  end
end
