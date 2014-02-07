task :default => :build

desc "Test everything"
task :test

desc "Build everything"
task :build

def lib(name)
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
  task :build => "build:#{name}"
end

def build(name)
  lib(name)

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
  task :build => "build:#{name}"
end

def test(name)
  testrs = File.join("src", name, "test.rs")
  return unless File.exist?(testrs)

  testbin = File.join("test", name)
  file testbin => testrs do
    sh "rustc", testrs, "-o", "test/#{name}", "--test"
  end
  task "build:#{name}" => testbin

  namespace :test do
    desc "Test #{name} crate"
    task(name => testbin) { sh testbin }
  end

  task :test => "test:#{name}"
end

FileList['src/*'].each do |path|
  name = path.split('/').last
  build(name)
  test(name)
end

task :clean do
  FileList['lib/*.{rlib,d}', 'test/*'].each do |f|
    FileUtils.rm_rf(f)
  end
end
