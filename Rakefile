# encoding: utf-8

require 'bundler/setup'
require 'yaml'
require 'twitter'

def read_config(config)
  config_file = YAML.load(IO.read('config.yml'))
  config.consumer_key = config_file['consumer_key']
  config.consumer_secret = config_file['consumer_secret']
  config.access_token = config_file['access_token']
  config.access_token_secret = config_file['access_token_secret']
end

def streaming_client
  Twitter::Streaming::Client.new(&method(:read_config))
end

def rest_client
  Twitter::REST::Client.new(&method(:read_config))
end

desc 'Search doet in Twitter'
task :search do
  stream = streaming_client
  client = rest_client
  begin
    stream.filter(track: '됬 -rt') do |object|
      next unless object.is_a?(Twitter::Tweet)
      retry_count = 3
      begin
        retry_count -= 1
        next if retry_count < 0
        reply_screen_name = object.user.screen_name
        reply_to = object.id
        update_string = "@#{reply_screen_name} 됐"

        print "Updating '#{update_string}' in reply to #{reply_to}..."
        client.update(update_string, in_reply_to_status_id: reply_to)
        puts 'done.'
      rescue Twitter::Error::DuplicateStatus
        puts 'Twitter::Error::DuplicateStatus'
      rescue Twitter::Error::TooManyRequests => error
        puts 'Twitter::Error::TooManyRequests: Sleep' \
             " #{error.rate_limit_reset_in} seconds..."
        sleep error.rate_limit.reset_in + 1
        retry
      end
    end
  rescue Twitter::Error::Unauthorized
    puts 'Unauthorized OAuth access token and secret.' \
         ' Please update your config.yml.'
  end
end
