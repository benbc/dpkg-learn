require 'erb'

versions = %w[0.1 0.2 0.3]
def package(version) "out/dpkg-learn_#{version}_all.deb" end
def package_files(version) "tmp/package-#{version}" end

task :default => :run
task :run => versions.map { |v| package(v) } do
  rm_rf 'tmp/log'
  dpkg "purge dpkg-learn"
  dpkg "install #{package('0.1')}"
  dpkg "remove dpkg-learn"
  dpkg "install #{package('0.2')}"
  dpkg "install #{package('0.3')}"
  dpkg "remove dpkg-learn"
  dpkg "purge dpkg-learn"
  sh "cat tmp/log"
end

versions.each do |version|
  file package(version) => ['out', 'tmp', 'src/control'] do
    root = package_files(version)
    recreate root
    control_files = "#{root}/DEBIAN"
    mkdir control_files
    cp 'src/control', "#{control_files}/control"
    substitute "#{control_files}/control", binding
    %w[preinst postinst prerm postrm].each do |script|
      file = "#{control_files}/#{script}"
      write(file, script_content)
      chmod 0755, file
    end
    sh "dpkg-deb --build #{root} out"
  end
end

task :clean do
  rm_rf 'tmp'
  rm_rf 'out'
end

directory 'out'
directory 'tmp'

def dpkg(command)
  sh "echo #{command} >>tmp/log"
  sh "sudo dpkg --#{command}"
end

def substitute(file, binding)
  content = ERB.new(File.read(file)).result(binding)
  write(file, content)
end

def write(file, content)
  File.open(file, 'w') { |f| f.write(content) }
end

def script_content
  <<EOF
#!/bin/sh -eu

script=`basename $0`
script=${script#dpkg-learn.}

echo "  $script $*" >>tmp/log
EOF
end

def recreate(dir)
  rm_rf dir
  mkdir_p dir
end
