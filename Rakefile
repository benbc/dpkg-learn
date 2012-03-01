require 'erb'

def version() '0.1' end
package = "out/dpkg-learn_#{version}_all.deb"
package_files = 'tmp/package'

def files_in(directory)
  Dir.glob("#{directory}/**/*")
end

task :default => :run
task :run => package

file package => ['out', package_files] do
  sh "dpkg-deb --build #{package_files} out"
end

file package_files => ['tmp', *files_in('src')] do
  recreate package_files
  cp_r 'src/.', "#{package_files}"
  substitute "#{package_files}/DEBIAN/control"
end

task :clean do
  rm_rf 'tmp'
  rm_rf 'out'
end

directory 'out'
directory 'tmp'

def substitute(file)
  content = ERB.new(File.read(file)).result(binding)
  File.open(file, 'w') { |f| f.write(content) }
end

def recreate(dir)
  rm_rf dir
  mkdir_p dir
end

