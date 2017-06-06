# Installing Dependencies

Install [`ruby`](https://www.ruby-lang.org/en/documentation/installation/).

Here are the installation steps if you use MacOS or Ubuntu.

MacOS:

    brew install ruby

Ubuntu:

    sudo apt-get install ruby

Then install [`bundler`](http://bundler.io), then [`Jekyll`](https://jekyllrb.com) via `bundle install`.

    gem install bundler
    bundle install     # Installs Jekyll, see Gemfile

# Viewing the Website

Get Jekyll to build the website and to start a web server:

    jekyll serve --incremental

Open a web browser and point to URL `localhost:4000`.
