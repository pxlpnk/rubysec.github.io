#############################
# Rebuild advisory Markdown #
#############################
require 'net/http'
require 'erb'
require 'yaml'

namespace :advisories do
  file '_advisories' do
    system 'git clone --depth 1 https://github.com/rubysec/ruby-advisory-db _advisories'
  end

  desc 'Updates the advisory db'
  task :update => '_advisories' do
    Dir.chdir('_advisories') { sh 'git pull' }
  end

  desc 'Regenerate the advisory posts'
  task :generate => :update do
    Rake::FileList["ruby-advisory-db/gems/*/*.yml"].each do |advisory_path|
      advisory = YAML.load_file(advisory_path)

      id   = if advisory['cve'] then "CVE-#{advisory['cve']}"
             else                    "OSVDB-#{advisory['osvdb']}"
             end
      slug = "#{advisory['date']}-#{id}"
      post = File.join('advisories','_posts',"#{slug}.md")

      File.open(post,'w') do |file|
        header = {
          'layout'     => 'advisory',
          'title'      => "#{id}: #{advisory['title']}",
          'comments'   => false,
            'categories' => [advisory['gem'], advisory['framework']].compact,
            'advisory'   => advisory
        }

        YAML.dump(header,file)
        file.puts '---'
      end
    end
  end
end

task :advisories => 'advisories:generate'
task :default => :advisories
