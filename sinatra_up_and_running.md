Sinatra: Up and Running
=======================

A short and concise guide on creating a web app or service using the Sinatra
library by Alan Harris and Konstantin Haase.

Taking the Stage
================

Sinatra is a minimalistic DSL for making websites, web services, and web apps in
Ruby.  It is not a framework.  It does not implement MVC.

You can build Sinatra apps in two modes: **classic** and **modular**.  Classic
mode is simpler, but it adds methods to the Object namespace and won't work
well if you ship the app in a gem.  Modular mode works well for shipping whole
apps in gems or combining multiple Sinatra apps.

Install with:

    $ gem install sinatra
    $ gem install thin

The Thin webserver is recommended for running Sinatra apps.  If Thin isn't
there, Sinatra will try to use Mongrel.  If there's no Mongrel, it will default
to WEBrick.

A full application in classic mode looks like this:

    require 'sinatra'

    get '/' do
      "Hello, world!"
    end

Save that into `server.rb` and run it with `ruby server.rb`.  Open it by going
to `http://localhost:4567`.

Sinatra wraps around Rack, which is a modular interface to multiple webservers.
The developer gets a nice DSL to work with.  Routes are written like
`verb "route" do...` and get matched in a top-down order.

If you do a `POST`, you'll get a 404 back:

    $ curl "localhost:4567" -d "foo=bar"

Now let's make a rock, paper, scissors game in the file `game.rb`:

    require 'sinatra'

    before do
      content_type :txt
      @defeat = {rock: :scissors, paper: :rock, scissors: :paper}
      @throws = @defeat.keys
    end

    get '/throw/:type' do
      player_throw = params[:type].to_sym
      if !@throws.include?(player_throw)
        halt 403, "You must throw one of the following: #{@throws}"
      end

      computer_throw = @throws.sample
      if player_throw == computer_throw
        "You tied, try again!"
      elsif computer_throw == @defeat[player_throw]
        "Nicely done; #{player_throw} beats #{computer_throw}"
      else
        "Ouch; #{computer_throw} beats #{player_throw}."
      end
    end

Now just run it with `ruby game.rb` and you can make throws like this:

    $ curl "localhost:4567/throw/rock"
    $ curl "localhost:4567/throw/paper"
    $ curl "localhost:4567/throw/scissors"

Fundamentals
============

A Peek Behind the Curtain
=========================

Modular Applications
====================

Hands On: Your Own Blog Engine
==============================
