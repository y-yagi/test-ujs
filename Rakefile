# frozen_string_literal: true

require "rake/testtask"
require "fileutils"
require "open3"

desc "Default Task"
task default: "test:ujs"

namespace :test do
  desc "Run tests for rails-ujs"
  task :ujs do
    begin
      FileUtils.rm_rf("log")
      Dir.mkdir("log")
      pid = spawn("bundle exec rackup test/ujs/config.ru -p 4567 -s puma", pgroup: true)

      start_time = Time.now

      loop do
        break if system("lsof -i :4567", 1 => File::NULL)

        if Time.now - start_time > 5
          puts "Timed out after 5 seconds"
          exit 1
        end
      end

      system("bundle exec ruby qunit-selenium-runner.rb http://localhost:4567/")
      status = $?.exitstatus
    ensure
      Process.kill("KILL", -pid) if pid
      FileUtils.rm_rf("log")
    end

    exit status
  end
end

namespace :ujs do
  desc "Starts the test server"
  task :server do
    system "bundle exec rackup test/ujs/config.ru -p 4567 -s puma"
  end
end
