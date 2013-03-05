require "rake"
require "rspec/core/rake_task"

$LOAD_PATH.unshift File.expand_path("../lib", __FILE__)
require "vmc/version"

def xsh(*args)
  p args
end

RSpec::Core::RakeTask.new(:spec)
task :default => :spec

namespace :deploy do
  def last_staging_sha
    `git rev-parse latest-staging`.strip
  end

  def last_release_sha
    `git rev-parse latest-release`.strip
  end

  def last_staging_ref_was_released?
    last_staging_sha == last_release_sha
  end

  task :staging, :version do |_, args|
    sh "gem bump --push #{"--version #{args.version}" if args.version}" if last_staging_ref_was_released?
    sh "git tag -f latest-staging"
    sh "git push origin :latest-staging"
    sh "git push origin latest-staging"
  end

  task :candidate do
    sh "git fetch"
    sh "git checkout latest-staging"
    sh "gem release"
    sh "git tag -f v#{VMC::VERSION}"
    sh "git tag -f latest-release"
    sh "git push origin :latest-release"
    sh "git push origin latest-release"
  end

  task :release do
    version = VMC::VERSION.sub(/\.rc\d+/, "")

    p [:version, version]

    xsh "git checkout latest-release -b release-v#{version}"
    xsh "gem bump --version #{version}"
    xsh "git checkout master"
    xsh "git merge release-v#{version}"
    xsh "gem release"
    xsh "git tag -f v#{version}"
    xsh "git tag -f latest-release"
    xsh "git tag -f latest-stable"
    xsh "git push origin master :latest-release :latest-stable latest-release latest-stable v#{version}"
  end
end
