task :default => :build

desc "Test everything"
task :test

desc "Build everything"
task :build

def libs_tasks(name)
  librs  = File.join("src", name, "lib.rs")
  return unless File.exist?(librs)

  arch = `rustc -v | tail -n1 | awk '{ print $2 }'`.chomp
  path = File.join("lib", arch)
  file = `rustc --crate-file-name #{librs}`.chomp
  libf = File.join(path, file)

  file libf => librs do
    sh "rustc", librs, "--out-dir", path
  end

  namespace :build do
    desc "Build #{name} crate"
    task name => libf
  end

  multitask :build => "build:#{name}"
end

def main_tasks(name)
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
  file testbin => ["test/", testrs] do
    sh "rustc", testrs, "-o", "test/#{name}", "--test"
  end

  namespace :test do
    desc "Test #{name} crate"
    task(name => testbin) { sh testbin }
  end

  multitask :test => "test:#{name}"
end

# A regular directory("test") conflicts with the
# test task, so I'm doing this instead. o_O
task "test/" do
  mkdir_p "test" unless File.exists?("test")
end

FileList['src/*'].each do |path|
  name = path.split('/').last
  main_tasks(name)
  libs_tasks(name)
  test_tasks(name)
end

task :clean do
  FileList['lib/**/*.rlib', 'test/*'].each { |f| rm_rf(f) }
end
