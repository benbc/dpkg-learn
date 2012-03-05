require 'erb'

def log() 'tmp/log' end
versions = %w[0.1 0.2 0.3]
def package(version) "out/dpkg-learn_#{version}_all.deb" end
def package_files(version) "tmp/package-#{version}" end

task :default => :run
task :run => versions.map { |v| package(v) } do
  rm_rf log
  dpkg "purge dpkg-learn"
  dpkg "install #{package('0.1')}"
  dpkg "purge dpkg-learn"
  dpkg "install #{package('0.1')}"
  dpkg "remove dpkg-learn"
  dpkg "purge dpkg-learn"
  dpkg "install #{package('0.1')}"
  dpkg "install #{package('0.1')}"
  dpkg "remove dpkg-learn"
  dpkg "install #{package('0.2')}"
  dpkg "install #{package('0.3')}"
  dpkg "purge dpkg-learn"
  sh "cat #{log}"
end

versions.each do |version|
  file package(version) => ['out', 'tmp', 'src/control'] do
    root = package_files(version)
    rm_rf root

    control_files = "#{root}/DEBIAN"
    mkdir_p control_files
    cp 'src/control', "#{control_files}/control"
    substitute "#{control_files}/control", binding
    %w[preinst postinst prerm postrm].each do |script|
      file = "#{control_files}/#{script}"
      write(file, script_content(version))
      chmod 0755, file
    end

    mkdir_p "#{root}/usr/share/dpkg-learn"
    touch "#{root}/usr/share/dpkg-learn/file-#{version}"

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
  sh "echo #{command} >>#{log}"
  sh "sudo dpkg --#{command}"
end

def substitute(file, binding)
  content = ERB.new(File.read(file)).result(binding)
  write(file, content)
end

def write(file, content)
  File.open(file, 'w') { |f| f.write(content) }
end

def script_content(version)
  <<EOF
#!/bin/sh -eu

script=`basename $0`
script=${script#dpkg-learn.}

echo "  $script-#{version} $*" >>#{log}

if [ ! -e /usr/share/dpkg-learn ]; then
  files='<missing>'
else
  files=`ls /usr/share/dpkg-learn`
  if [ ! files ]; then
    files='<empty>'
  fi
fi
echo "$files" | sed 's/^/    /g'  >>#{log}
EOF
end
