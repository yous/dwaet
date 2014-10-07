# Dwaet (됐)

No **doet**, it's **dwaet**.

**됬**이 아니라 **됐**입니다.

## Feature

1. Search tweets containing doet:
    - '됬'
    - '됫' except [words containing '됫'](http://krdic.naver.com/search.nhn?kind=keyword&query=%2A%EB%90%AB%2A)
2. Mention '됐' to them.

## Installation

Clone this repository:

``` sh
git clone https://github.com/yous/dwaet.git
cd dwaet
```

Install prerequisite gems:

``` sh
bundle install
```

Update `config.yml` with your Twitter OAuth access token and secret:

``` yaml
consumer_key:        'YOUR_CONSUMER_KEY'
consumer_secret:     'YOUR_CONSUMER_SECRET'
access_token:        'YOUR_ACCESS_TOKEN'
access_token_secret: 'YOUR_ACCESS_SECRET'
```

## Usage

``` sh
rake search    # Search doet in Twitter
rake timeline  # Search doet in timeline
rake mentions  # Search doet in mentions
```

## License

Copyright (c) 2014 ChaYoung You. See [LICENSE.txt](LICENSE.txt) for further details.
