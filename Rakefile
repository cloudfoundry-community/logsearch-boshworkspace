namespace :plugins do
  desc "Update logsearch plugins"
  task update: [:clone_or_update_repos, :install_deps, :build_and_test, :update_templates]

  task :update_templates do
    template = <<-EOF.gsub(/      /, '')
      properties:
        logstash_parser:
          plugins: |
    EOF
    template += '      <%=' # prevent bosh_cli from stripping '<%' http://git.io/OKyMtw
    template += filter_config.gsub(/^/, '      ')
    IO.write('templates/logsearch/plugins.yml', template)
  end

  task :clone_or_update_repos do
    FileUtils.mkdir_p('plugins')
    Dir.chdir 'plugins' do
      plugins.each do |plugin_name, plugin_git|
        git_repo, git_ref = plugin_git[:git], plugin_git[:ref]
        if File.exists?(plugin_name)
          Dir.chdir(plugin_name) { sh "git pull origin #{git_ref}" }
        else
          sh "git clone -b #{git_ref} #{git_repo} #{plugin_name}"
          sh "cd #{plugin_name}; git submodule update --init"
        end
      end
    end
  end

  task :install_deps do
    in_plugin_dirs { sh './bin/install_deps.sh' }
  end

  task :build_and_test do
    in_plugin_dirs { sh './bin/build-and-test.sh' }
  end

  def plugins
    {
      'logsearch-filters-common' => {
        git: 'https://github.com/logsearch/logsearch-filters-common',
        ref: 'master'
      },
      'logsearch-filters-cf' => {
        git: 'https://github.com/logsearch/logsearch-filters-cf',
        ref: 'master'
      },
      'logsearch-for-websites' => {
        git: 'https://github.com/drnic/logsearch-for-websites',
        ref: 'consistent_api'
      }
    }
  end

  def in_plugin_dirs
    plugins.each do |plugin_name, plugin_git|
      Dir.chdir "plugins/#{plugin_name}" do
        Bundler.with_clean_env { yield }
      end
    end
  end

  def filter_config
    plugins.map do |plugin_name|
      "plugins/#{plugin_name}/target/**/*.conf"
    end.map do |files|
      Dir[files].map { |file| puts file; IO.read(file) }
    end.flatten.join('')
  end
end
