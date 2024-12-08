# Action Cable

Action cable is mainly used for integrating websocket communication. We need to learn some terminolagy before going deep into it.

- **Connections**: These form the foundation for client-server relationship. A single Action Cable server can handle multiple connection instances. It has one connection instance per WebSocket connection. A single user may have multiple WebSockets open to your application if they use multiple browser tabs or devices.

- **Consumer**: Client of a  websocket is called a consumer.

- **Channel**: Each Channel can encapsulate a logical unit of a work, similar to what a controller does. A consumer can subscribe to multiple channels.

- **Subscriber**: When a consumer is subscribed to a channel then they act as a subscriber.

- **Pub/Sub**: Publish-Subscribe refers to a message queue paradigm whereby senders of information (publishers), send data to an abstract class of recipients (subscribers), without specifying individual recipients. Action Cable uses this approach to communicate between the server and many clients.

- **Broadcasting**: A broadcasting is a pub/sub link where anything transmitted by the broadcaster is sent directly to the channel subscribers who are streaming that named broadcasting. Each channel can be streaming zero or more broadcastings.


# Working of Action Cable

As we already know this is used to integrate web-socket communication, there are 2 parts for subscribing and broadcasting. We need to subscribe from client-side so that we can receive data from that particular channel or send info to server.

Consider we are having a channel named chat_channel. 

To generate a new channel you can use generate in terminal

`bin/rails g channel chat`. Ths will create new chat_channel.

So, in server-side we have a channel named `chat_channnel.rb`. The file looks something like this:

```ruby
class ChatChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chat_#{params[:user_id]}"
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end
end

```

So if you see here, once a user is subscribed to a channel then subscribed method gets triggered in the server side. In here we are calling the stream_from with a name so that, we can use that name to broadcast data to the client. Now we have seen in server how to define a channel. Now you can also see chat_channel.js file in javascripts folder. it will look something like this:

```javascript
import consumer from "channels/consumer"

consumer.subscriptions.create({ channel: "ChatChannel", user_id: 1}, {
  connected() {
    console.log(`connected`)
    // Called when the subscription is ready for use on the server
  },

  disconnected() {
    console.log(`disconnected`)
    // Called when the subscription has been terminated by the server
  },

  received(data) {
    console.log(`received ${data}`)
    // Called when there's incoming data on the websocket for this channel
  }
});
```

So, as you see here we are importing he consumer which is reponsible for creating the sockets(This is the deault one for action cables).

Here you are using `consumer.subscriptions.create` to add the subscription to specific channel. For example you can use `consumer.subscriptions.create("RandomChannel")`. 
In the example we are using chat_channel so we need to use "ChatChannel" and it needs one argument naemd user_id, so it will look like:
`consumer.subscriptions.create({channel: "ChatChannel", user_id: 1})`, here channel is mandatory one and others are arguments.

This js file path is added in `index.js` so that this cable is automatically gets runned and keep the sockets alive.

So when you run `consumer.subscriptions.create` this will call the server channel and it runs subscribed method and there it will run Stream_from. Then, in server whenever we run the broadcast like `ActionCable.server.broadcast("chat_#{params[:user_id]}", {data: "Data broadcast for #{params[:user_id]}"})`. Now whome ever subscribed to that channel will receive the data `{data: "Data broadcast for #{params[:user_id]}`.

You can also call methods that are implemented in the server side channel from client side channel.

consider you have a method named `message(data)` in server side. It will look like:

```ruby
def message(data)
  ActionCable.server.broadcast("chat_#{data.user_id}", {data: data.message})
end
```

Now in client side add a method named sendMessage, it looks like this:

```javascript
const chat_channel = consumer.subscriptions.create({ channel: "ChatChannel", user_id: 1}, {
  connected() {
    console.log(`connected`)
    // Called when the subscription is ready for use on the server
  },

  disconnected() {
    console.log(`disconnected`)
    // Called when the subscription has been terminated by the server
  },

  received(data) {
    console.log(`received ${data}`)
    // Called when there's incoming data on the websocket for this channel
  }

  // This method is used to send the data to server from client.
  sendMessage(id, message) {
    this.perform("message", { user_id: id, message: message });
  }
});
```

Now using `chat_channel` we can call `sendMessage` method to send the data from client to server using action cable `chatChannel.sendMessage(1, "Sending message")`. In this way we can create methods to and fro communication.

