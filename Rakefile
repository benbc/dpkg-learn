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
    mkdir_p "#{root}/DEBIAN"
    cp 'src/control', "#{root}/DEBIAN/control"
    substitute "#{root}/DEBIAN/control", binding
    %w[preinst postinst prerm postrm].each do |script|
      File.open("#{root}/DEBIAN/#{script}", 'w') { |f| f.write(script_content) }
      chmod 0755, "#{root}/DEBIAN/#{script}"
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
  File.open(file, 'w') { |f| f.write(content) }
end

def script_content
  <<SCRIPT
#!/bin/sh -eu
echo $0 $* >>tmp/log
SCRIPT
end

def recreate(dir)
  rm_rf dir
  mkdir_p dir
end
