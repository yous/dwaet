# encoding: utf-8

require 'bundler/setup'
require 'yaml'
require 'twitter'

desc 'Start Dwaet'
task :start do
  config_file = YAML.load(IO.read('config.yml'))
  stream = Twitter::Streaming::Client.new do |config|
    config.consumer_key = config_file['consumer_key']
    config.consumer_secret = config_file['consumer_secret']
    config.access_token = config_file['access_token']
    config.access_token_secret = config_file['access_token_secret']
  end
  client = Twitter::REST::Client.new do |config|
    config.consumer_key = config_file['consumer_key']
    config.consumer_secret = config_file['consumer_secret']
    config.access_token = config_file['access_token']
    config.access_token_secret = config_file['access_token_secret']
  end
  begin
    stream.filter(track: '됬 -rt') do |object|
      next unless object.is_a?(Twitter::Tweet)
      begin
        reply_screen_name = object.user.screen_name
        reply_to = object.id
        print "Updating '@#{reply_screen_name} 됐' in reply to #{reply_to}..."
        client.update("@#{reply_screen_name} 됐", in_reply_to_status_id: reply_to)
        puts 'done.'
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

task default: :start
