import 'package:flutter/material.dart';
import 'package:socket_io_client/socket_io_client.dart' as IO;

void main() => runApp(MaterialApp(home: ChatScreen()));

class ChatScreen extends StatefulWidget {
  @override
  _ChatScreenState createState() => _ChatScreenState();
}

class _ChatScreenState extends State<ChatScreen> {
  late IO.Socket socket;
  TextEditingController _controller = TextEditingController();
  List<String> messages = [];

  @override
  void initState() {
    super.initState();
    socket = IO.io('http://YOUR_SERVER_IP:3000', <String, dynamic>{
      'transports': ['websocket'],
    });
    socket.on('chat message', (data) {
      setState(() => messages.add(data));
    });
  }

  void sendMessage() {
    socket.emit('chat message', _controller.text);
    setState(() => messages.add("You: ${_controller.text}"));
    _controller.clear();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Basic Chat")),
      body: Column(children: [
        Expanded(child: ListView.builder(itemCount: messages.length, itemBuilder: (context, index) => Text(messages[index]))),
        Row(children: [
          Expanded(child: TextField(controller: _controller)),
          IconButton(icon: Icon(Icons.send), onPressed: sendMessage)
        ])
      ]),
    );
  }
}
