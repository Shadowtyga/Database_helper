# Database_helper
//Organisation
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

class DatabaseHelper {

  DatabaseHelper._init();//Constructeur privé
   static final instance= DatabaseHelper._init();//création du singleton pattern

  static Database? _database;//Création de la variable statique



  Future<Database> get database async {
    if (_database != null) return _database!;
    _database = await _initDB('example.db');//création de la méthode privé initDB
    return _database!;
  }

  Future<Database> _initDB(String filePath) async {
    final dbPath = await getDatabasesPath();
    final path = join(dbPath, filePath);

    return await openDatabase(path, version: 1, onCreate: _createDB);
    //Création de l'ouverture de la base de donnée
    //_createDB est une fonction de rappel nécéssaire ?
  }

  Future _createDB(Database db, int version) async {
    const idType = 'INTEGER PRIMARY KEY AUTOINCREMENT';
    const textType = 'TEXT NOT NULL';

    await db.execute('''
    CREATE TABLE example (
      id $idType,
      title $textType
    )
    ''');
  }
//Sqflite me conduit à utilser les map pour mieux gérer la BD
  Future<int> insert(String title) async {
    final db = await instance.database;

    final data = {'title': title};
    return await db.insert('example', data);
  }

  Future<List<Map<String, dynamic>>> readAll() async {
    final db = await instance.database;

    return await db.query('example');
  }

  Future<int> update(int id, String title) async {
    final db = await instance.database;

    final data = {'title': title};
    return await db.update('example', data, where: 'id = ?', whereArgs: [id]);
  }

  Future<int> delete(int id) async {
    final db = await instance.database;

    return await db.delete('example', where: 'id = ?', whereArgs: [id]);
  }

  Future close() async {
    final db = await instance.database;
    db.close();
  }
}

