require 'erb'

versions = %w[0.1 0.2 0.3]
def package(version) "out/dpkg-learn_#{version}_all.deb" end
def package_files(version) "tmp/package-#{version}" end

def files_in(directory)
  Dir.glob("#{directory}/**/*")
end

task :default => :run
task :run => versions.map { |v| package(v) } do
  rm_rf 'tmp/dpkg-learn'
  sh "sudo dpkg --purge dpkg-learn"
  sh "sudo dpkg --install #{package('0.1')}"
  sh "sudo dpkg --remove dpkg-learn"
  sh "sudo dpkg --install #{package('0.2')}"
  sh "sudo dpkg --install #{package('0.3')}"
  sh "sudo dpkg --remove dpkg-learn"
  sh "sudo dpkg --purge dpkg-learn"
  sh "cat tmp/dpkg-learn"
end

versions.each do |version|
  file package(version) => ['out', 'tmp', *files_in('src')] do
    root = package_files(version)
    recreate root
    cp_r 'src/.', root
    substitute "#{root}/DEBIAN/control", binding
    sh "dpkg-deb --build #{root} out"
  end
end

task :clean do
  rm_rf 'tmp'
  rm_rf 'out'
end

directory 'out'
directory 'tmp'

def substitute(file, binding)
  content = ERB.new(File.read(file)).result(binding)
  File.open(file, 'w') { |f| f.write(content) }
end

def recreate(dir)
  rm_rf dir
  mkdir_p dir
end
