namespace :addons do
  desc "Update logsearch plugins"
  task update: [:fetch_addons, :update_templates]

  task :update_templates do
    puts "===> Combining addon filters into templates/logsearch/logstash-filters.yml..."
    template = <<-EOF.gsub(/      /, '')
      properties:
        logstash_parser:
          filters: |
    EOF
    template += '      <%=' # prevent bosh_cli from stripping '<%' http://git.io/OKyMtw
    template += filter_config.gsub(/^/, '      ')
    IO.write('templates/logsearch/logstash-filters.yml', template)
  end

  task :fetch_addons do
    puts "===> Downloading addons..."
    FileUtils.rm_rf('addons')
    FileUtils.mkdir_p('addons')
    Dir.chdir 'addons' do
      addons.each do |addon_name, addon_data|
        logstash_filters = addon_data[:logstash_filters]
        FileUtils.mkdir_p(addon_name)
        sh "cd #{addon_name}; curl --silent --remote-name #{logstash_filters}"
      end
    end
  end

  def addons
    {
      'logsearch-for-cloudfoundry' => {
        logstash_filters: 'https://raw.githubusercontent.com/logsearch/logsearch-filters-cf/new-logsearch-addon-structure/target/logstash-filters-default.conf'
      } # ,
      # 'logsearch-for-websites' => {
      #    logstash_filters: 'https://raw.githubusercontent.com/logsearch/logsearch-for-websites/v0.5/target/logstash-filters-default.conf'
      # }
    }
  end

  def filter_config
    addons.map do |addon_name|
      "addons/#{addon_name[0]}/logstash-filters-*.conf"
    end.map do |files|
      Dir[files].map { |file| puts file; IO.read(file) }
    end.flatten.join('')
  end
end
