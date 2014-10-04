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

def mention_dwaet(rest, filter = nil)
  lambda do |object|
    next if filter && filter.call(object)
    retry_count = 3
    begin
      retry_count -= 1
      next if retry_count < 0
      reply_screen_name = object.user.screen_name
      reply_to = object.id
      update_string = "@#{reply_screen_name} 됐"

      print "Updating '#{update_string}' in reply to #{reply_to}..."
      rest.update(update_string, in_reply_to_status_id: reply_to)
      puts 'done.'
    rescue Twitter::Error::DuplicateStatus => error
      warn error.class
    rescue Twitter::Error::TooManyRequests => error
      warn "#{error.class}: Sleep #{error.rate_limit_reset_in} seconds..."
      sleep error.rate_limit.reset_in + 1
      retry
    end
  end
end

def main_loop(&block)
  streaming = streaming_client
  rest = rest_client
  block.call(streaming, rest)
rescue Twitter::Error::Unauthorized => error
  warn "#{error.class}: Unauthorized OAuth access token and secret." \
       ' Please update your config.yml.'
rescue Twitter::Error::ServerError => error
  warn "#{error.class}: Retrying in 10 seconds..."
  sleep 10
  retry
end

desc 'Search doet in Twitter'
task :search do
  main_loop do |streaming, rest|
    filter = ->(x) { x.is_a?(Twitter::Tweet) && !x.retweet? }
    streaming.filter(track: '됬', &mention_dwaet(rest, filter))
  end
end

desc 'Search doet in timeline'
task :timeline do
  main_loop do |streaming, rest|
    filter = ->(x) { x.is_a?(Twitter::Tweet) && !x.retweet? }
    streaming.user(&mention_dwaet(rest, filter))
  end
end

desc 'Search doet in mentions'
task :mentions do
  main_loop do |_streaming, rest|
    since_id = nil
    loop do
      options = { count: Twitter::REST::Timelines::DEFAULT_TWEETS_PER_REQUEST }
      options[:since_id] = since_id if since_id
      mentions = rest.mentions_timeline(options).reverse
      mentions.each(&mention_dwaet(rest))

      since_id = mentions.last.id
      sleep 60
    end
  end
end
