namespace :filters do
  desc "Update logsearch filters"
  task update: [:clone_or_update_repos, :install_deps, :build_and_test, :update_templates]

  task :update_templates do
    template = <<-EOF.gsub(/      /, '')
      properties:
        logstash_parser:
          filters: |
    EOF
    template += '      <%=' # prevent bosh_cli from stripping '<%' http://git.io/OKyMtw
    template += filter_config.gsub(/^/, '      ')
    IO.write('templates/logsearch/filters.yml', template)
  end

  task :clone_or_update_repos do
    Dir.chdir 'tmp' do
      filters.each do |filter|
        if File.exists?(filter)
          Dir.chdir(filter) { sh 'git pull origin master' }
        else
          sh "git clone https://github.com/logsearch/#{filter}"
          sh "cd #{filter}; git submodule update --init"
        end
      end
    end
  end

  task :install_deps do
    in_filter_dirs { sh './bin/install_deps.sh' }
  end

  task :build_and_test do
    in_filter_dirs { sh './bin/build-and-test.sh' }
  end

  def in_filter_dirs
    filters.each do |filter|
      Dir.chdir "tmp/#{filter}" do
        Bundler.with_clean_env { yield }
      end
    end
  end

  def filters
    %w(logsearch-filters-common logsearch-filters-cf)
  end

  def filter_config
    filters.map { |f| "tmp/#{f}/target/**/*.conf" }.map do |files|
      Dir[files].map { |file| puts file; IO.read(file) }
    end.flatten.join('')
  end
end
