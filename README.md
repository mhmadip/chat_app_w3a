# chat_app_w3
Week 3 Chat App OOP2 (20 April 2022)

Our goals for this week are:

- Activate Chat Screen with some UI improvement for the chat conversation (This is done by using Firestore database to store chat details and Streams to reterive chat details into the Screen).
- Add then show Progress Bar when user clicks ok Login and Register.
- Change Firestore Database Security Rules to not allow others messsing with our database then test these new rules.





Week 2 Chat App OOP2 ( 5 April 2022)

Our goals for last week were done:

1- Add authentication feature using Firebase Authentication (email and phone).
2- To add Firebase and use Firestore to store chat messages.


![App Brewery Banner](https://github.com/londonappbrewery/Images/blob/master/AppBreweryBanner.png)


# Flash Chat ⚡️

## Our Goal

The objective of this tutorial is to learn how to incorporate Firebase into our Flutter apps. We'll be using Firebase Cloud Firestore as well as the Firebase authentication package to equip our app with a cloud-based NoSQL database and secure authentication methods.


## What you will create

We’re going to build a modern messaging app where users can sign up and log in to chat.

![Finished App](https://github.com/londonappbrewery/Images/blob/master/flash_chat_flutter_demo.gif)

## What you will learn

- How to incorporate Firebase into your Flutter projects.
- How to use Firebase authentication to register and sign in users.
- How to create beautiful animations using the Flutter Hero widget.
- How to create custom aniamtions using Flutter's animation controller.
- Learn all about mixins and how they differ from superclasses.
- Learn about Streams and how they work.
- Learn to use ListViews to build scrolling views.
- How to use Firebase Cloud Firestore to store and retrieve data on the fly.



>This is a companion project to The App Brewery's Complete Flutter Development Bootcamp, check out the full course at [www.appbrewery.co](https://www.appbrewery.co/)

![End Banner](https://github.com/londonappbrewery/Images/blob/master/readme-end-banner.png)


class _ChatScreenState extends State<ChatScreen> {
//list of needed variables, put inside the _ChatScreenState
final _auth = FirebaseAuth.instance;
late User loggedInUser;
late String messageText;
final messageTextController = TextEditingController();
final _firestore = FirebaseFirestore.instance;

@override
void initState() {
// TODO: implement initState
super.initState();
getCurrentUser();
}

void getCurrentUser() async {
try {
final user = await _auth.currentUser;
if (user != null) {
loggedInUser = user;
print(loggedInUser.email);
}
} catch (e) {
print(e);
}
}

@override
Widget build(BuildContext context) {
return Scaffold(
appBar: AppBar(
leading: null,
actions: <Widget>[
IconButton(
icon: const Icon(Icons.close),
onPressed: () async {
//Implement logout functionality
await _auth.signOut();
Navigator.pop(context);
}),
],
title: const Text('⚡️Chat'),
backgroundColor: Colors.lightBlueAccent,
),
body: SafeArea(
child: Column(
mainAxisAlignment: MainAxisAlignment.spaceBetween,
crossAxisAlignment: CrossAxisAlignment.stretch,
children: <Widget>[
StreamBuilder<QuerySnapshot>(
stream: _firestore.collection('messages').snapshots(),
builder: (context, snapshot) {
if (!snapshot.hasData) {
return const Center(
child: CircularProgressIndicator(),
);
}
final messages = snapshot.data?.docs;
List<Text> messageWidgets = [];
for (var message in messages!) {
final messageText = message['text'];
final messageSender = message['sender'];
final messageWidget =
Text('$messageText from $messageSender');
messageWidgets.add(messageWidget);
}
return Column(
children: messageWidgets,
);
}),
Container(
decoration: kMessageContainerDecoration,
child: Row(
crossAxisAlignment: CrossAxisAlignment.center,
children: <Widget>[
Expanded(
child: TextField(
onChanged: (value) {
//Do something with the user input.
messageText = value;
},
decoration: kMessageTextFieldDecoration,
),
),
TextButton(
onPressed: () {
//Implement send functionality.
messageTextController.clear();
_firestore.collection('messages').add(
{'sender': loggedInUser.email, 'text': messageText});
},
child: const Text(
'Send',
style: kSendButtonTextStyle,
),
),
],
),
),
],
),
),
);
}
}
