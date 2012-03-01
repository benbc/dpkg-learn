version = '0.1'
package = "out/dpkg-learn_#{version}_all.deb"

def files_in(directory)
  Dir.glob("#{directory}/**/*")
end

task :default => :run

task :run => package

file package => ['out', *files_in('src')] do
  sh "dpkg-deb --build src out"
end

directory 'out'
