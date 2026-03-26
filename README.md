import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        primarySwatch: Colors.indigo,
        scaffoldBackgroundColor: Colors.grey[100],
        textTheme: TextTheme(
          bodyMedium: TextStyle(color: Colors.black87),
        ),
      ),
      home: MainScreen(),
    );
  }
}

class MainScreen extends StatefulWidget {
  @override
  _MainScreenState createState() => _MainScreenState();
}

class _MainScreenState extends State<MainScreen> {
  int _selectedIndex = 0;

  final List<Widget> _pages = [
    HomePage(),
    ProfilePage(),
    SettingsPage(),
  ];

  void _onItemTapped(int index) {
    setState(() => _selectedIndex = index);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _pages[_selectedIndex],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _selectedIndex,
        selectedItemColor: Colors.indigo,
        unselectedItemColor: Colors.grey,
        onTap: _onItemTapped,
        items: [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: "Home"),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: "Profile"),
          BottomNavigationBarItem(icon: Icon(Icons.settings), label: "Settings"),
        ],
      ),
    );
  }
}

/// ===================== HOME PAGE WITH ANIMATIONS =====================
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage>
    with SingleTickerProviderStateMixin {
  double _opacity = 1.0;
  double _size = 100;

  late AnimationController _controller;
  late Animation<double> _staggerOpacity;
  late Animation<double> _staggerSize;

  @override
  void initState() {
    super.initState();

    _controller = AnimationController(vsync: this, duration: Duration(seconds: 2));

    _staggerOpacity = Tween(begin: 0.0, end: 1.0).animate(
      CurvedAnimation(parent: _controller, curve: Interval(0.0, 0.5)),
    );

    _staggerSize = Tween(begin: 60.0, end: 160.0).animate(
      CurvedAnimation(parent: _controller, curve: Interval(0.5, 1.0)),
    );
  }

  void _toggleFade() => setState(() => _opacity = _opacity == 1 ? 0 : 1);
  void _animateSize() => setState(() => _size = _size == 100 ? 170 : 100);
  void _startStagger() => _controller.forward(from: 0);

  Route _createRoute() {
    return PageRouteBuilder(
      pageBuilder: (_, __, ___) => HeroPage(),
      transitionsBuilder: (_, animation, __, child) {
        return SlideTransition(
          position: Tween(begin: Offset(1, 0), end: Offset.zero)
              .animate(CurvedAnimation(parent: animation, curve: Curves.easeInOut)),
          child: child,
        );
      },
    );
  }

  Widget sectionCard({required String title, required Widget child}) {
    return Container(
      margin: EdgeInsets.symmetric(horizontal: 16, vertical: 10),
      padding: EdgeInsets.all(16),
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(14),
        boxShadow: [BoxShadow(color: Colors.black12, blurRadius: 8, offset: Offset(0, 4))],
      ),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.center,
        children: [
          Text(title,
              style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold, color: Colors.indigo)),
          SizedBox(height: 12),
          child,
        ],
      ),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Home"), centerTitle: true),
      body: ListView(
        children: [
          sectionCard(
            title: "Hero Animation",
            child: GestureDetector(
              onTap: () => Navigator.push(context, _createRoute()),
              child: Hero(
                tag: "box",
                child: Container(
                  height: 100,
                  width: 100,
                  decoration: BoxDecoration(color: Colors.indigo, borderRadius: BorderRadius.circular(12)),
                  child: Center(
                      child: Text("Open",
                          style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold))),
                ),
              ),
            ),
          ),
          sectionCard(
            title: "Fade Animation",
            child: Column(
              children: [
                AnimatedOpacity(
                  opacity: _opacity,
                  duration: Duration(seconds: 1),
                  child: Container(
                    height: 80,
                    width: 80,
                    decoration: BoxDecoration(color: Colors.redAccent, borderRadius: BorderRadius.circular(10)),
                    child: Center(child: Text("Fade", style: TextStyle(color: Colors.white))),
                  ),
                ),
                SizedBox(height: 10),
                ElevatedButton(onPressed: _toggleFade, child: Text("Toggle Fade")),
              ],
            ),
          ),
          sectionCard(
            title: "Implicit Animation",
            child: Column(
              children: [
                AnimatedContainer(
                  width: _size,
                  height: _size,
                  duration: Duration(seconds: 1),
                  curve: Curves.easeInOut,
                  decoration: BoxDecoration(color: Colors.green, borderRadius: BorderRadius.circular(12)),
                  child: Center(child: Text("Resize", style: TextStyle(color: Colors.white))),
                ),
                SizedBox(height: 10),
                ElevatedButton(onPressed: _animateSize, child: Text("Animate Size")),
              ],
            ),
          ),
          sectionCard(
            title: "Staggered Animation",
            child: Column(
              children: [
                AnimatedBuilder(
                  animation: _controller,
                  builder: (_, __) {
                    return Opacity(
                      opacity: _staggerOpacity.value,
                      child: Container(
                        width: _staggerSize.value,
                        height: _staggerSize.value,
                        decoration: BoxDecoration(color: Colors.purple, borderRadius: BorderRadius.circular(12)),
                        child: Center(child: Text("Stagger", style: TextStyle(color: Colors.white))),
                      ),
                    );
                  },
                ),
                SizedBox(height: 10),
                ElevatedButton(onPressed: _startStagger, child: Text("Start Animation")),
              ],
            ),
          ),
          SizedBox(height: 30),
        ],
      ),
    );
  }
}
/// ==================== ANOTHER PAGE =====================
class AnotherPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Another Page")),
      body: Padding(
        padding: EdgeInsets.all(20),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start, // left align all texts
          children: [
            Text(
              "Name: Chris Nicolai L. Cañeda",
              style: TextStyle(fontSize: 15, fontWeight: FontWeight.bold),
              textAlign: TextAlign.left,
            ),
            SizedBox(height: 10), // spacing between lines
            Text(
              "Year: 2A",
              style: TextStyle(fontSize: 15, fontWeight: FontWeight.bold),
              textAlign: TextAlign.left,
            ),
            SizedBox(height: 10),
            Text(
              "Course: BSIT",
              style: TextStyle(fontSize: 15, fontWeight: FontWeight.bold),
              textAlign: TextAlign.left,
            ),
          ],
        ),
      ),
    );
  }
}
/// ===================== PROFILE PAGE =====================
class ProfilePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Profile"), centerTitle: true),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            GestureDetector(
              onTap: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(builder: (context) => AnotherPage()),
                );
              },
              child: CircleAvatar(
                radius: 50,
                backgroundColor: Colors.indigo,
                child: Icon(Icons.person, size: 50, color: Colors.white),
              ),
            ),
            SizedBox(height: 20),
            SizedBox(
              width: 200,
              child: Column(
                   crossAxisAlignment: CrossAxisAlignment.center,
                children: [
                  Text("Nico", style: TextStyle(fontSize: 22, fontWeight: FontWeight.bold)),
                  SizedBox(height: 10),
                  Text("ccpd2005@gmail.com", style: TextStyle(color: Colors.grey[700])),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
/// ===================== SETTINGS PAGE =====================
class SettingsPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Settings"), centerTitle: true),
      body: ListView(
        padding: EdgeInsets.all(16),
        children: [
          ListTile(leading: Icon(Icons.notifications), title: Text("Notifications")),
          ListTile(leading: Icon(Icons.lock), title: Text("Privacy")),
          ListTile(leading: Icon(Icons.palette), title: Text("Theme")),
          ListTile(leading: Icon(Icons.info), title: Text("About")),
        ],
      ),
    );
  }
}

/// ===================== HERO PAGE =====================
class HeroPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Hero Page")),
      body: Center(
        child: Hero(
          tag: "box",
          child: Container(
            width: 220,
            height: 220,
            decoration: BoxDecoration(color: Colors.indigo, borderRadius: BorderRadius.circular(16)),
            child: Center(child: Text("Bulaga", style: TextStyle(color: Colors.white, fontSize: 18))),
          ),
        ),
      ),
    );
  }
}
