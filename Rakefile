require 'nanoc3/tasks'

desc "Compile the site"
task :compile do
  sh 'nanoc compile -l -V'
end

desc "Run a webserver for viewing the site locally"
task :server => :compile do
  sh 'nanoc view'
end

desc "Push the current master branch to origin"
task :push_master do
  sh 'git push origin master'
end

desc "Publish the static site to github pages"
task :publish => [:push_master, :clean] do
  FileUtils.rm_r('output') if File.exist?('output')

  sh "nanoc compile"

  ENV['GIT_DIR'] = File.expand_path(`git rev-parse --git-dir`.chomp)
  old_sha = `git rev-parse refs/remotes/origin/gh-pages`.chomp
  Dir.chdir('output') do
    ENV['GIT_INDEX_FILE'] = gif = '/tmp/dev.gh.i'
    ENV['GIT_WORK_TREE'] = Dir.pwd
    File.unlink(gif) if File.file?(gif)
    `git add -A`
    tsha = `git write-tree`.strip
    puts "Created tree   #{tsha}"
    if old_sha.size == 40
      csha = `echo 'boom' | git commit-tree #{tsha} -p #{old_sha}`.strip
    else
      csha = `echo 'boom' | git commit-tree #{tsha}`.strip
    end
    puts "Created commit #{csha}"
    puts `git show #{csha} --stat`
    puts "Updating gh-pages from #{old_sha}"
    `git update-ref refs/heads/gh-pages #{csha}`
    sh 'git push origin gh-pages'
  end
end

task :default => :server
