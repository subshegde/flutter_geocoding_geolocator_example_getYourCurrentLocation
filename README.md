# flutter_geocoding_geolocator_example_getYourCurrentLocation


Add this in your AndroidMenifest.xml : 
---------------------------------------------------
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />


Add this for ios , in info.plist
-----------------------------------------------
<key>NSLocationWhenInUseUsageDescription</key>
<string>Reason why app needs location</string>

------------------------------------------------------------

// add this in pubspec.yaml
 geocoding: ^2.0.0
  geolocator: ^7.1.0



import 'package:flutter/material.dart';
import 'package:geocoding/geocoding.dart';
import 'package:geolocator/geolocator.dart';

class GeocodeWidget extends StatefulWidget {
  @override
  _GeocodeWidgetState createState() => _GeocodeWidgetState();
}

class _GeocodeWidgetState extends State<GeocodeWidget> {
  Position? _position;
  String _address = '';

  @override
  void initState() {
    super.initState();
  }

  Future<void> _getCurrentLocation() async {
    try {
      Position position = await Geolocator.getCurrentPosition(
          desiredAccuracy: LocationAccuracy.best);
      setState(() {
        _position = position;
      });
      await _getAddressFromCoordinates();
    } catch (e) {
      print('Error getting location: $e');
    }
  }

  Future<String> _getAddressFromCoordinates() async {
    if (_position == null) return '';

    try {
      List<Placemark> placemarks = await placemarkFromCoordinates(
          _position!.latitude, _position!.longitude);

      if (placemarks != null && placemarks.isNotEmpty) {
        Placemark placemark = placemarks[0];
        String address =
            '${placemark.street}, ${placemark.locality},${placemark.administrativeArea}, ${placemark.country}';
        setState(() {
          _address = address;
        });
        return _address;
      } else {
        return '';
      }
    } catch (e) {
      print('Error fetching address: $e');
      return '';
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Geolocator Demo'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            if (_position != null)
              Column(
                children: <Widget>[
                  // Text(
                  //     'Latitude: ${_position!.latitude}, Longitude: ${_position!.longitude}'),
                  // SizedBox(height: 10),
                  Text('Address: $_address'),
                ],
              ),
            ElevatedButton(
              onPressed: _getCurrentLocation,
              child: Text('Get Current Location'),
            ),
          ],
        ),
      ),
    );
  }
}


