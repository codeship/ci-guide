# base on latest ruby base image
FROM ruby:2.5.0

# update and install dependencies
RUN apt-get update -qq
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential libpq-dev nodejs apt-utils

# setup app folder
WORKDIR /app

# copy over Gemfile and Gemfile.lock to image's working directory, then run `bundle install`
COPY Gemfile Gemfile.lock ./
RUN bundle install --jobs 20 --retry 5

COPY . /app
