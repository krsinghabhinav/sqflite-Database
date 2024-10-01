# sqflite-Database

import 'dart:io';
import 'package:flutter/material.dart';
import 'package:path/path.dart';
import 'package:path_provider/path_provider.dart';
import 'package:sqflite/sqflite.dart';

class HostelDatabase {
  /// Singleton
  HostelDatabase._();

  static final HostelDatabase instance = HostelDatabase._();

  factory HostelDatabase() {
    return instance;
  }

  /// Table and column names
  static const String TABLE_HOSTEL = 'hostel';
  static const String COLUMN_HOSTEL_S_NO = 's_no';
  static const String COLUMN_HOSTEL_HOSTELNAME = 'hostelname';
  static const String COLUMN_HOSTEL_ADDRESS = 'address';
  static const String COLUMN_HOSTEL_IMAGE = 'image';
  static const String COLUMN_HOSTEL_RATING = 'rating';
  static const String COLUMN_HOSTEL_PRICE = 'price';
  static const String COLUMN_HOSTEL_MOBILE = 'mobile';
  static const String COLUMN_HOSTEL_DESCRIPTION = 'description';
  static const String COLUMN_HOSTEL_LATITUDE = 'latitude';
  static const String COLUMN_HOSTEL_LONGITUDE = 'longitude';
  static const String COLUMN_HOSTEL_SECURITY = 'security';
  static const String COLUMN_HOSTEL_HOSTELFOR = 'hostelfor';
  static const String COLUMN_HOSTEL_SINGLEROOMPRICE = 'singleroomprice';
  static const String COLUMN_HOSTEL_DOUBLEROOMPRICE = 'doublebedprice';
  static const String COLUMN_HOSTEL_TRIPLEROOMPRICE = 'triplebedprice';
  Database? myDB;

  /// Open or create the database
  Future<Database?> getDB() async {
    if (myDB != null) {
      return myDB;
    } else {
      myDB = await _openDB();
      return myDB;
    }
  }

  Future<Database?> _openDB() async {
    Directory directoryPath = await getApplicationDocumentsDirectory();
    String dbPath = join(directoryPath.path, "hostel.db");
    return await openDatabase(dbPath, version: 1, onCreate: (db, version) {
      return db.execute("CREATE TABLE $TABLE_HOSTEL("
          "$COLUMN_HOSTEL_S_NO INTEGER PRIMARY KEY AUTOINCREMENT, "
          "$COLUMN_HOSTEL_HOSTELNAME TEXT, "
          "$COLUMN_HOSTEL_ADDRESS TEXT, "
          "$COLUMN_HOSTEL_IMAGE TEXT, "
          "$COLUMN_HOSTEL_RATING TEXT, "
          "$COLUMN_HOSTEL_PRICE TEXT, "
          "$COLUMN_HOSTEL_MOBILE TEXT, "
          "$COLUMN_HOSTEL_DESCRIPTION TEXT, "
          "$COLUMN_HOSTEL_LATITUDE REAL, "
          "$COLUMN_HOSTEL_LONGITUDE REAL,$COLUMN_HOSTEL_HOSTELFOR TEXT, $COLUMN_HOSTEL_SECURITY TEXT, $COLUMN_HOSTEL_SINGLEROOMPRICE TEXT, $COLUMN_HOSTEL_DOUBLEROOMPRICE TEXT, $COLUMN_HOSTEL_TRIPLEROOMPRICE TEXT)");
    });
  }

  /// Insert data
  Future<bool?> addData({
    required String hostelname,
    required String hosteladdress,
    required String hostelImage,
    required String hostelrating,
    required String hostelprice,
    required String hostelmobile,
    required String hosteldesc,
    required double hostellatitude,
    required double hostellongitude,
    required String hostelsecurity,
    required String hostelhostelfor,
    required String hostelsingleroomprice,
    required String hosteldoublebedprice,
    required String hosteltriplebedprice,
  }) async {
    var db = await getDB();
    int rowsAffected = await db!.insert(TABLE_HOSTEL, {
      COLUMN_HOSTEL_HOSTELNAME: hostelname,
      COLUMN_HOSTEL_ADDRESS: hosteladdress,
      COLUMN_HOSTEL_IMAGE: hostelImage,
      COLUMN_HOSTEL_RATING: hostelrating,
      COLUMN_HOSTEL_PRICE: hostelprice,
      COLUMN_HOSTEL_MOBILE: hostelmobile,
      COLUMN_HOSTEL_DESCRIPTION: hosteldesc,
      COLUMN_HOSTEL_LATITUDE: hostellatitude,
      COLUMN_HOSTEL_LONGITUDE: hostellongitude,
      COLUMN_HOSTEL_HOSTELFOR: hostelhostelfor,
      COLUMN_HOSTEL_SECURITY: hostelsecurity,
      COLUMN_HOSTEL_SINGLEROOMPRICE: hostelsingleroomprice,
      COLUMN_HOSTEL_DOUBLEROOMPRICE: hosteldoublebedprice,
      COLUMN_HOSTEL_TRIPLEROOMPRICE: hosteltriplebedprice,
    });
    return rowsAffected > 0; // true if data inserted
  }

  /// Read all data
  Future<List<Map<String, dynamic>>> getAllData() async {
    var db = await getDB();
    return await db!.query(TABLE_HOSTEL);
  }

  /// Update a note
  // Future<bool?> updateNotes(
  //     {required String mtitle, required String mdesc, required int sno}) async {
  //   var db = await getDB();
  //   int rowsAffected = await db!.update(
  //     TABLE_NOTE,
  //     {COLUMN_NOTE_TITLE: mtitle, COLUMN_NOTE_DESC: mdesc},
  //     where: "$COLUMN_NOTE_S_NO = ?",
  //     whereArgs: [sno],
  //   );
  //   return rowsAffected > 0;
  // }

  /// Delete a note
  // Future<bool?> deleteNotes({required int sno}) async {
  //   var db = await getDB();
  //   int rowsAffected = await db!.delete(
  //     TABLE_NOTE,
  //     where: "$COLUMN_NOTE_S_NO = ?",
  //     whereArgs: [sno],
  //   );
  //   return rowsAffected > 0;
  // }

  /// Remove data from favorites
  Future<bool?> removeData(String hostelname) async {
    var db = await getDB();
    int rowsAffected = await db!.delete(
      TABLE_HOSTEL,
      where: "$COLUMN_HOSTEL_HOSTELNAME = ?",
      whereArgs: [hostelname],
    );
    return rowsAffected > 0; // true if data removed
  }

  /// Check if a hostel is already in favorites
  Future<bool> isFavorite(String hostelname) async {
    var db = await getDB();
    final List<Map<String, dynamic>> result = await db!.query(
      TABLE_HOSTEL,
      where: "$COLUMN_HOSTEL_HOSTELNAME = ?",
      whereArgs: [hostelname],
    );
    return result.isNotEmpty; // true if the hostel exists
  }


 /// Update a note
  // Future<bool?> updateNotes(
  //     {required String mtitle, required String mdesc, required int sno}) async {
  //   var db = await getDB();
  //   int rowsAffected = await db!.update(
  //     TABLE_NOTE,
  //     {COLUMN_NOTE_TITLE: mtitle, COLUMN_NOTE_DESC: mdesc},
  //     where: "$COLUMN_NOTE_S_NO = ?",
  //     whereArgs: [sno],
  //   );
  //   return rowsAffected > 0;
  // }

  /// Delete a note
  // Future<bool?> deleteNotes({required int sno}) async {
  //   var db = await getDB();
  //   int rowsAffected = await db!.delete(
  //     TABLE_NOTE,
  //     where: "$COLUMN_NOTE_S_NO = ?",
  //     whereArgs: [sno],
  //   );
  //   return rowsAffected > 0;
  // }

  
}




=====================================================================


import 'package:flutter/material.dart';
import 'package:get/get.dart';
import 'package:hostelpedia/View/Screen/hosteldetails_screen.dart';
import '../../database/favoritesStore.dart';

class FavoritesPage extends StatefulWidget {
  const FavoritesPage({super.key});

  @override
  State<FavoritesPage> createState() => _FavoritesPageState();
}

class _FavoritesPageState extends State<FavoritesPage> {
  List<Map<String, dynamic>> favoriteHostels = [];
  final HostelDatabase hostelRefDB = HostelDatabase();

  void getFavoriteData() async {
    favoriteHostels = await hostelRefDB.getAllData();
    setState(() {});
  }

  @override
  void initState() {
    super.initState();
    getFavoriteData();
  }

  @override
  Widget build(BuildContext context) {
    getFavoriteData();

    return Scaffold(
      appBar: AppBar(
        title: const Text("Favorite Hostels"),
      ),
      body: favoriteHostels.isEmpty
          ? const Center(
              child: CircularProgressIndicator(),
            )
          : ListView.builder(
              itemCount: favoriteHostels.length,
              itemBuilder: (context, index) {
                return InkWell(
                  onTap: () {
                    Get.to(
                      HostelDetailsScreen(
                        hostelName: favoriteHostels[index]
                            [HostelDatabase.COLUMN_HOSTEL_HOSTELNAME],
                        hostelImage: favoriteHostels[index]
                            [HostelDatabase.COLUMN_HOSTEL_IMAGE],
                        images: [],
                        rating: favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_RATING] ??
                            "rating",
                        description: favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_DESCRIPTION] ??
                            "description",
                        security: favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_SECURITY] ??
                            "security",
                        mobile: favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_MOBILE] ??
                            "mobile",
                        address: favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_ADDRESS] ??
                            "address",
                        latitude: favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_LATITUDE] ??
                            0,
                        longitude: favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_LONGITUDE] ??
                            0,
                        hostelfor: favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_HOSTELFOR] ??
                            "hostelfor",
                        singleroomprice: favoriteHostels[index][
                                HostelDatabase.COLUMN_HOSTEL_SINGLEROOMPRICE] ??
                            "singleroomprice",
                        doublebedprice: favoriteHostels[index][
                                HostelDatabase.COLUMN_HOSTEL_DOUBLEROOMPRICE] ??
                            "doublebedprice",
                        triplebedprice: favoriteHostels[index][
                                HostelDatabase.COLUMN_HOSTEL_TRIPLEROOMPRICE] ??
                            "triplebedprice",
                      ),
                    );
                  },
                  child: Card(
                    elevation: 4,
                    margin:
                        const EdgeInsets.symmetric(horizontal: 8, vertical: 5),
                    child: Row(
                      crossAxisAlignment: CrossAxisAlignment.start,
                      children: [
                        ClipRRect(
                          borderRadius: BorderRadius.circular(8.0),
                          child: Image.network(
                            favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_IMAGE],
                            height: 160, // Adjusted height
                            width: 140, // Adjusted width
                            fit: BoxFit.cover,
                          ),
                        ),
                        const SizedBox(width: 10),
                        Expanded(
                          child: Padding(
                            padding: const EdgeInsets.symmetric(vertical: 5.0),
                            child: Column(
                              crossAxisAlignment: CrossAxisAlignment.start,
                              mainAxisAlignment: MainAxisAlignment.spaceBetween,
                              children: [
                                Text(
                                  favoriteHostels[index][HostelDatabase
                                          .COLUMN_HOSTEL_HOSTELNAME] ??
                                      "Hostel Name",
                                  style: const TextStyle(
                                    fontSize: 16,
                                    fontWeight: FontWeight.w600,
                                  ),
                                  maxLines: 2,
                                  overflow: TextOverflow.ellipsis,
                                ),
                                const SizedBox(height: 5),
                                Row(
                                  children: [
                                    const Icon(Icons.star,
                                        color: Colors.amber, size: 20),
                                    const SizedBox(width: 5),
                                    Text(
                                      '${favoriteHostels[index][HostelDatabase.COLUMN_HOSTEL_RATING] ?? '---'} | Rating',
                                      style: const TextStyle(fontSize: 14),
                                    ),
                                  ],
                                ),
                                const SizedBox(height: 5),
                                Row(
                                  children: [
                                    const Icon(Icons.location_on,
                                        size: 20, color: Colors.red),
                                    const SizedBox(width: 5),
                                    Expanded(
                                      child: Text(
                                        favoriteHostels[index][HostelDatabase
                                                .COLUMN_HOSTEL_ADDRESS] ??
                                            'Location',
                                        style: const TextStyle(fontSize: 14),
                                        maxLines: 3,
                                        overflow: TextOverflow.ellipsis,
                                      ),
                                    ),
                                  ],
                                ),
                                const SizedBox(height: 5),
                                Row(
                                  children: [
                                    const Icon(Icons.currency_rupee, size: 18),
                                    const SizedBox(width: 5),
                                    Text(
                                      favoriteHostels[index][HostelDatabase
                                              .COLUMN_HOSTEL_PRICE] ??
                                          'Price',
                                      style: const TextStyle(
                                        fontSize: 18,
                                        fontWeight: FontWeight.bold,
                                      ),
                                    ),
                                  ],
                                ),
                              ],
                            ),
                          ),
                        ),
                      ],
                    ),
                  ),
                );
              },
            ),
    );
  }
}
==========================================================================

 bool isFavorite = false; // Initial state for favorite
  Future<void> _checkIfFavorite() async {
    isFavorite = await hostelRefDB.isFavorite(widget.hostelName);
    setState(() {}); // Update the UI after checking
  }

  @override
  void initState() {
    super.initState();
    _checkIfFavorite();
    selectedImage = widget.hostelImage;
  }

===================================================================================

  Row(
                        children: [
                          GestureDetector(
                            onTap: () async {
                              // Toggle the favorite status
                              setState(() {
                                isFavorite =
                                    !isFavorite; // Change the favorite status
                              });

                              if (isFavorite) {
                                // If it's now marked as favorite, add to favorites
                                await hostelRefDB.addData(
                                  hostelname: widget.hostelName,
                                  hosteladdress: widget.address,
                                  hostelImage: widget.hostelImage,
                                  hostelrating: widget.rating,
                                  hostelprice: widget.singleroomprice,
                                  hostelmobile: widget.mobile,
                                  hosteldesc: widget.description,
                                  hostellatitude: widget.latitude,
                                  hostellongitude: widget.longitude,
                                  hostelhostelfor: widget.hostelfor,
                                  hostelsecurity: widget.security,
                                  hostelsingleroomprice: widget.singleroomprice,
                                  hosteldoublebedprice: widget.doublebedprice,
                                  hosteltriplebedprice: widget.triplebedprice,
                                );

                                ScaffoldMessenger.of(context).showSnackBar(
                                  SnackBar(
                                    content: Text('Added to Favorites'),
                                  ),
                                );
                              } else {
                                await hostelRefDB.removeData(widget.hostelName);
                                ScaffoldMessenger.of(context).showSnackBar(
                                  SnackBar(
                                    content: Text('Removed from Favorites'),
                                  ),
                                );
                              }
                            },
                            child: Icon(
                              Icons.favorite,
                              color: isFavorite
                                  ? Colors.red
                                  : Colors
                                      .white, // Set color based on favorite status
                              size: MediaQuery.of(context).size.width * 0.08,
                            ),
                          ),
                          SizedBox(
                              width: MediaQuery.of(context).size.width * 0.02),
                          GestureDetector(
                            onTap: () {
                              ScaffoldMessenger.of(context).showSnackBar(
                                const SnackBar(
                                    content: Text('Share icon clicked')),
                              );
                            },
                            child: Icon(
                              Icons.share_rounded,
                              color: const Color.fromARGB(255, 235, 126, 38),
                              size: MediaQuery.of(context).size.width * 0.08,
                            ),
                          ),
                        ],
                      ),
