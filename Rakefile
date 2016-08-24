$: << File.join(File.dirname(__FILE__), "lib")

require 'rspec/core/rake_task'
require 'securerandom'

task :default => [:spec_all]

desc "initial project setup"
task :project_setup do
  Rake::Task["gen_secrets"].invoke
  Rake::Task["commit_template"].invoke
  puts "\e[32mSuccess!\e[0m"
end

desc "run all tests"
task(:spec_all) do
  sh "grunt spec"
  Rake::Task["spec"].invoke
end

desc "run ruby test suite"
task(:spec) do
  RSpec::Core::RakeTask.new { |t| t.verbose = false }
end

desc "generates secret keys"
task :gen_secrets do
  puts "\e[34mgenerating secret keys...\e[0m"

  File.open("./config/secret_key", "w+") do |file|
    file << SecureRandom.hex(24) + "\n"
  end

  File.open("./config/admin_key", "w+") do |file|
    file << SecureRandom.hex(24) + "\n"
  end
end

desc "sets git commit template"
task :commit_template do
  puts "\e[34msetting git template...\e[0m"
  sh "git config commit.template './.github/commit.template'"
end
