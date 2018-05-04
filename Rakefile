# coding: utf-8
require 'rake'
require 'rspec/core/rake_task'

task :default => :preview

desc "ビルドしてS3にデプロイする"
task :deploy do
  sh "bundle exec jekyll build"
  sh "bundle exec s3_website push"
end

desc "プレビュー(インクリメンタルモード)"
task :preview do
  sh "bundle exec jekyll server -I"
end
