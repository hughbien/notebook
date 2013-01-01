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

This chapter starts out with some HTTP fundamentals.  See
`http_the_definitive_guide.md` for more details.

Routes are commonly defined like:

    get '/' do
      "Triggered via GET"
    end

    post '/' do
      "Triggered via POST"
    end

    put '/' do
      "Triggered via PUT"
    end

    delete '/' do
      "Triggered via DELETE"
    end

    patch '/' do
      "Triggered via PATCH"
    end

    options '/' do
      "Triggered via OPTIONS"
    end

Parameters in routes are specified with a colon and accessed with `params`:

    get '/:name' do
      "Hello, #{params[:name]}"
    end

Data from POST, PUT, or PATCH request bodies or URL query string can also be
accessed via `params`.

The splat character can be used as a greedy match:

    get '/*' do
      # /foo/bar => ["foo/bar"]
      # /foo/bar/baz/bop => ["foo/bar/baz/bop"]
      params[:splat]
    end

Regular expressions may also be used:

    get %r{/(sp|gr)eedy} do
      "You got caught!"
    end

You can halt a request with `halt` which takes a response code.  You can `pass`
a request to the next matching route.  You can also `redirect` which defaults to
a temporary redirect (use 301 for permanent, defaults to 302).

    get '/halt' do
      halt(500) if some_condition
    end

    get '/pass' do
      pass if some_condition
    end

    get '/redirect' do
      redirect 'http://www.google.com' if some_condition
    end

    get '/redirect/permanently' do
      redirect 'http://www.google.com', 301
    end

Sinatra uses the `public` subdirectory for static files.  If you have the file
`static.html` in it, the route `/static.html` will automatically direct you to
the file.  It will even override any `get "/static.html"` in your app.
Configure this directory like:

    set :public_folder, File.dirname(__FILE__) + '/your_custom_location'

Sinatra can use inline or external templates.  Here's an example of inline:

    get '/index' do
      erb :index
    end

    __END__

    @@index
    <!DOCTYPE html>
    <html>
      ...
    </html>

For external templates, put the HTML into a file `views/index.erb`.  You can
change the default subfolder using the `set :views` option.  Some built in
templating engines are: Erb, Haml, and Erubis.  Data can be passed into views
by assigning and using instance variables.

You can also use before/after filters.  Without a parameter, they'll work on
all requests.  They also accept route parameters just like the HTTP verbs.

    before do
      puts "Before all requests."
    end

    before '/some/*' do
      puts "Before some requests."
    end

    after do
      puts "After all requests."
    end

    after '/some/*" do
      puts "After some requests."
    end

404s and 500s are so common, Sinatra provides helpers to handle them gracefully.

    not_found do
      content_type :txt
      "Whoops!  You requested a route that's not available."
    end

For 500s:

    configure do
      set :show_exceptions, false
    end

    error do
      content_type :txt
      "Y U NO WORK?"
    end

Use `headers` for custom response headers:

    get '/' do
      headers 'X-Custom-Value' => 'Custom HTTP header',
              'X-Custom-Value2' => 'bar'
      "Hello, World!"
    end

The `request` object has more info about incoming requests:

    get '/env' do
      content_type :txt
      request.env.map { |e| e.to_s + "\n" }
    end

    get '/methods' do
      content_type :txt
      request.methods.map { |m| m.to_s + "\n" }
    end

Some helper methods can be used to set common cache headers:

    require 'uuid'

    expires 3600, :public, :must_revalidate
    etag UUID.new.generate
    etag UUID.new.generate, :weak

Cookie based sessions look like this:

    configure do
      enable :sessions
      set :session_secret, 'your_custom_value_here' # default is random per start
    end

    before do
      content_type :txt
    end

    get '/set' do
      session[:foo] = Time.now
      "Session value set."
    end

    get '/fetch' do
      "Session value: #{session[:foo]}"
    end

    get '/logout' do
      session.clear
      redirect '/fetch'
    end

Session based cookies are cleared automatically when the browser closes.  To
use persistent cookies:

    response.set_cookie 'foo', 'bar'
    request.cookies['foo']
    response.delete_cookie 'foo'
    response.set_cookie 'foo', :value => 'bar', :path => '/'

Attachments can be sent downstream via `attachment` method which accepts a file
path.

A Peek Behind the Curtain
=========================

Modular Applications
====================

Hands On: Your Own Blog Engine
==============================
