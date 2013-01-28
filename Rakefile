require 'rake'
require 'yuicompressor'
require 'time'

SOURCE = "."
CONFIG = {
  'version' => "0.2.13",
  'themes' => File.join(SOURCE, "_includes", "themes"),
  'layouts' => File.join(SOURCE, "_layouts"),
  'posts' => File.join(SOURCE, "_posts"),
  'post_ext' => "md",
  'theme_package_version' => "0.1.0"
}

desc "Compile CSS files"
task :css do
  puts "Merging CSS"

  `rm static/css/style.css`
  `rm static/css/temp.css`

  %W{font-awesome syntax skeleton base layout}.each do |file|
    `cat ./static/css/#{file}.css >> ./static/css/temp.css`
  end

  # `mv ./static/css/temp.css ./static/css/style.css`
  # `yuicompressor ./static/css/temp.css > ./static/css/style.css`

  css = File.read('static/css/temp.css')
  File.open('static/css/style.css', 'w') do |file|
    file.write(YUICompressor.compress_css(css))
  end

  puts 'CSS dumped to ./static/css/style.css'
end

# Usage: rake post title="A Title" [date="2012-02-09"]
desc "Begin a new post in #{CONFIG['posts']}"
task :post do
  abort("rake aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
  title = ENV["title"] || "new-post"
  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  begin
    date = (ENV['date'] ? Time.parse(ENV['date']) : Time.now).strftime('%Y-%m-%d')
  rescue Exception => e
    puts "Error - date format must be YYYY-MM-DD, please check you typed it correctly!"
    exit -1
  end
  filename = File.join(CONFIG['posts'], "#{date}-#{slug}.#{CONFIG['post_ext']}")
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/-/,' ')}\""
    post.puts 'description: ""'
    post.puts "category: "
    post.puts "tags: []"
    post.puts "---"
  end
end

desc "Deploy site"
task :deploy do
  Rake::Task['css'].execute
  puts 'Comitting generated CSS'
  `git add static/css/style.css`
  `git commit -m 'Compressed CSS for deploy'`

  puts "Pushing to Github"
  `git push origin master`
end

task "Serve"
task :serve do
  Rake::Task['css'].execute

  `open http://localhost:4000`
  `jekyll --serve --no-pygments --auto`
end
