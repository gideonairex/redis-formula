require 'kitchen'
require 'fileutils'

namespace :test do
  config = Kitchen::Config.new
  config.instances.each do |instance|
    case instance.name
      when /vagrant/ 
        desc 'Run Test Kitchen in Vagrant'
        task :vagrant do
          Kitchen.logger = Kitchen.default_file_logger
          instance.test(:always)
        end
      when /aws/
        desc 'Run Test Kitchen in AWS'
        task :aws do
          # "diagnose" method is the only PUBLIC method which will give you a hash of merged configs
          formula = "#{config.loader.diagnose[:combined_config][:raw_data]["provisioner"]["formula"]}-formula"
          run_shaker = "salt-shaker shake root_formula=ministryofjustice/#{formula}"
          puts "Resolving formula dependencies: #{run_shaker}"
          sh run_shaker do |ok, res|
            if !ok
              abort "Failed: (status = #{res.exitstatus})"
            end
          end 
          Kitchen.logger = Kitchen.default_file_logger
          instance.test(:always)
        end
    end
  end
end

task default: ['test:vagrant']
