task :default => :build

desc "Test everything"
task :test

desc "Build everything"
task :build

def build_lib_tasks(name)
  librs  = File.join("src", name, "lib.rs")
  return unless File.exist?(librs)

  lib = "lib/" << `rustc --crate-file-name #{librs}`.chomp
  file lib => librs do
    sh "rustc", librs, "--out-dir", "lib"
  end
  namespace :build do
    desc "Build #{name} crate"
    task name => lib
  end
  multitask :build => "build:#{name}"
end

def build_tasks(name)
  build_lib_tasks(name)

  mainrs = File.join("src", name, "main.rs")
  return unless File.exist?(mainrs)

  mainbin = File.join("bin", name)
  file mainbin => mainrs do
    sh "rustc", mainrs, "-o", mainbin
  end

  namespace :build do
    desc "Build #{name} binary"
    task name => mainbin
  end
  multitask :build => "build:#{name}"
end

def test_tasks(name)
  testrs = File.join("src", name, "test.rs")
  return unless File.exist?(testrs)

  testbin = File.join("test", name)
  file testbin => [:ensure_test_dir, testrs] do
    sh "rustc", testrs, "-o", "test/#{name}", "--test"
  end
  task "build:#{name}" => testbin

  namespace :test do
    desc "Test #{name} crate"
    task(name => testbin) { sh testbin }
  end

  multitask :test => "test:#{name}"
end

# A regular directory("test") conflicts with the
# test task, so I'm doing this instead. o_O
task :ensure_test_dir do
  mkdir_p "test" unless File.exists?("test")
end

FileList['src/*'].each do |path|
  name = path.split('/').last
  build_tasks(name)
  test_tasks(name)
end

task :clean do
  FileList['lib/*.{rlib,d}', 'test/*'].each { |f| rm_rf(f) }
end
