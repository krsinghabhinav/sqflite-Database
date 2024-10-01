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
      return db.execute(
          "CREATE TABLE $TABLE_HOSTEL($COLUMN_HOSTEL_S_NO INTEGER PRIMARY KEY AUTOINCREMENT, $COLUMN_HOSTEL_HOSTELNAME TEXT, $COLUMN_HOSTEL_ADDRESS TEXT, $COLUMN_HOSTEL_IMAGE TEXT, $COLUMN_HOSTEL_RATING TEXT, $COLUMN_HOSTEL_PRICE TEXT)");
    });
  }

  /// Insert data
  Future<bool?> addData({
    required String hostelname,
    required String hosteladdress,
    required String hostelImage,
    required String hostelrating,
    required String hostelprice,
  }) async {
    var db = await getDB();
    int rowsAffected = await db!.insert(TABLE_HOSTEL, {
      COLUMN_HOSTEL_HOSTELNAME: hostelname,
      COLUMN_HOSTEL_ADDRESS: hosteladdress,
      COLUMN_HOSTEL_IMAGE: hostelImage,
      COLUMN_HOSTEL_RATING: hostelrating,
      COLUMN_HOSTEL_PRICE: hostelprice,
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
}

===============================================================================================

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
                return Card(
                  elevation: 4,
                  child: Container(
                    height: 140, // Adjust height based on need
                    child: Row(
                      crossAxisAlignment: CrossAxisAlignment.start,
                      children: [
                        ClipRRect(
                          // borderRadius: BorderRadius.circular(8.0),
                          child: Image.network(
                            favoriteHostels[index]
                                [HostelDatabase.COLUMN_HOSTEL_IMAGE],
                            height: 140,
                            width: 160,
                            fit: BoxFit.cover,
                          ),
                        ),
                        const SizedBox(width: 10),
                        Expanded(
                          child: Column(
                            crossAxisAlignment: CrossAxisAlignment.start,
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
                              // const SizedBox(height: 3),
                              Row(
                                children: [
                                  const Icon(Icons.star,
                                      color: Colors.amber, size: 20),
                                  const SizedBox(width: 5),
                                  Text(
                                    '${favoriteHostels[index][HostelDatabase.COLUMN_HOSTEL_RATING] ?? '---'} | Rating',
                                    style: const TextStyle(fontSize: 16),
                                  ),
                                ],
                              ),
                              // const SizedBox(height: 3),
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
                                      maxLines: 2,
                                      overflow: TextOverflow.ellipsis,
                                    ),
                                  ),
                                ],
                              ),
                              // const SizedBox(height: 3),
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
                      ],
                    ),
                  ),
                );
              },
            ),
    );
  }
}



