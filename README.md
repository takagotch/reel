### reel
---
https://github.com/celluloid/reel

```ruby
MyApp = Webmachine::Aplication.new do |app|
  app.routes do
    add ['*'], MyHome
  end
  app.configure do |config|
    config.ip = MYAPP_IP
    config.port = MYAPP_PORT
    config.adapter = :Reel
    config.adapter_options[:websocket_handler] = proc do |websoket|
      websocket << "hello!"
    end
  end
end
MyApp.run

require 'celluloid/autostart'
require 'reel'
Reel::Server::HTTP.supervise("0.0.0.0", 3000) do |connection|
  connection.each_request do |request|
    if request.websocket?
      puts "Client made a WebSocket request to: #{request.url}"
      websocket = request.websocket
      websocket << "Hello"
      websocket.close
    else
      puts "Client requested: #{request.method} #{request.url}"
      request.respond :ok, "Hello!"
    end
  end
end
sleep

require 'celluloid/autostart'
require 'reel'
class MyServer < Reel::Server::HTTP
  def initalize(host = "127.0.0.1", port = 3000)
    super(host, port, &method(:on_connection))
  end
  def on_connection(connection)
    connection.each_request do |request|
      if request.websocket?
        handle_websocket(request.websocket)
      else
        handle_request(request)
      end
    end
  end
  def handle_request(request)
    request.respond :ok, "Hello!"
  end
  def handle_websocket(sock)
    sock << "Hello everyone out there in WebSocket land!"
    sock.close
  end
end
MyServer.run
```

```
gem 'webmachine', git: 'git://github.com/seancribbs/webmachine-ruby.git'
```

```
```


