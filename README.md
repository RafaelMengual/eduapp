# eduapp
Aplicación Flutter con Floor y SQLite
📚 EduApp
EduApp es una aplicación educativa desarrollada en Flutter con almacenamiento local utilizando Floor (SQLite).
Permite registrar usuarios, iniciar sesión, visualizar cursos disponibles e inscribirse en ellos desde un entorno simple y totalmente funcional.
________________________________________
👩‍🏫 Información del proyecto
•	Nombre del Proyecto: EduApp
•	Programa: Desarrollo Móvil
•	Universidad: Universidad de La Guajira
•	Docente: Brayan Otero
•	Integrante del Grupo:
o	Rafael Ángel Mengual
________________________________________
⚙️ Tecnologías utilizadas
•	Flutter
•	Dart
•	SQLite (mediante Floor)
•	Android Studio / Visual Studio Code
________________________________________
📂 Estructura de la base de datos
La aplicación utiliza tres tablas:
1.	Usuario
o	idUsuario (PK)
o	nombreCompleto
o	correo
o	contrasena
2.	Curso
o	idCurso (PK)
o	nombreCurso
o	descripcion
3.	Inscripcion
o	idInscripcion (PK)
o	idUsuario (FK)
o	idCurso (FK)
o	fechaInscripcion
________________________________________
🚀 Pasos para ejecutar el proyecto
1️⃣ Instalar dependencias
Ejecutar en la raíz del proyecto:
flutter pub get
2️⃣ Generar el código de Floor
flutter packages pub run build_runner build
3️⃣ Ejecutar la aplicación
En Android o Windows:
flutter run
________________________________________
🗂 Publicación en GitHub
Este proyecto debe subirse a GitHub asegurando:
✔ Repositorio público
✔ Todos los integrantes agregados como colaboradores
✔ Commits de cada participante
________________________________________
📌 Funcionalidades principales
•	Registro de usuario
•	Inicio de sesión
•	Listado de cursos almacenados localmente
•	Inscripción de usuarios en cursos
•	Navegación entre pantallas
________________________________________
🔧 Requisitos previos
Antes de ejecutar se debe tener instalado:
•	Flutter SDK (3.x o superior)
•	Git
•	Android Studio o VSCode
•	Windows Developer Mode activado (para ejecutar en Windows)
________________________________________
👨‍💻 Autor
Rafael Ángel Mengual
Universidad de La Guajira – Programa Desarrollo Móvil
________________________________________
¡Gracias por revisar este proyecto!

import 'dart:async';
import 'package:flutter/material.dart';
import 'package:floor/floor.dart';
import 'package:path_provider/path_provider.dart';
import 'package:sqflite/sqflite.dart' as sqflite;

part 'main.g.dart'; // Generado automáticamente

// ===========================
// ENTIDADES (Tablas)
// ===========================

@Entity(tableName: 'Usuario')
class Usuario {
  @PrimaryKey(autoGenerate: true)
  final int? idUsuario;
  final String nombreCompleto;
  final String correo;
  final String contrasena;

  Usuario({
    this.idUsuario,
    required this.nombreCompleto,
    required this.correo,
    required this.contrasena,
  });
}

@Entity(tableName: 'Curso')
class Curso {
  @PrimaryKey(autoGenerate: true)
  final int? idCurso;
  final String nombreCurso;
  final String descripcion;

  Curso({
    this.idCurso,
    required this.nombreCurso,
    required this.descripcion,
  });
}

@Entity(
  tableName: 'Inscripcion',
  foreignKeys: [
    ForeignKey(
      childColumns: ['idUsuario'],
      parentColumns: ['idUsuario'],
      entity: Usuario,
    ),
    ForeignKey(
      childColumns: ['idCurso'],
      parentColumns: ['idCurso'],
      entity: Curso,
    ),
  ],
)
class Inscripcion {
  @PrimaryKey(autoGenerate: true)
  final int? idInscripcion;
  final int idUsuario;
  final int idCurso;
  final String fechaInscripcion;

  Inscripcion({
    this.idInscripcion,
    required this.idUsuario,
    required this.idCurso,
    required this.fechaInscripcion,
  });
}

// ===========================
// DAO (Data Access Object)
// ===========================

@dao
abstract class UsuarioDao {
  @Query('SELECT * FROM Usuario')
  Future<List<Usuario>> findAllUsuarios();

  @Query('SELECT * FROM Usuario WHERE correo = :correo')
  Future<Usuario?> findByCorreo(String correo);

  @insert
  Future<void> insertUsuario(Usuario usuario);
}

@dao
abstract class CursoDao {
  @Query('SELECT * FROM Curso')
  Future<List<Curso>> findAllCursos();

  @insert
  Future<void> insertCurso(Curso curso);

  @Query('DELETE FROM Curso')
  Future<void> deleteAll();
}

@dao
abstract class InscripcionDao {
  @Query('SELECT * FROM Inscripcion')
  Future<List<Inscripcion>> findAllInscripciones();

  @insert
  Future<void> insertInscripcion(Inscripcion inscripcion);
}

// ===========================
// BASE DE DATOS
// ===========================

@Database(version: 1, entities: [Usuario, Curso, Inscripcion])
abstract class AppDatabase extends FloorDatabase {
  UsuarioDao get usuarioDao;
  CursoDao get cursoDao;
  InscripcionDao get inscripcionDao;
}

// ===========================
// MAIN
// ===========================

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  final database = await $FloorAppDatabase.databaseBuilder('eduapp.db').build();

  // Insertar cursos iniciales
  final cursosExistentes = await database.cursoDao.findAllCursos();
  if (cursosExistentes.isEmpty) {
    await database.cursoDao.insertCurso(
      Curso(nombreCurso: 'Matemáticas', descripcion: 'Curso básico de álgebra'),
    );
    await database.cursoDao.insertCurso(
      Curso(
          nombreCurso: 'Ciencias',
          descripcion: 'Explora el mundo de la ciencia'),
    );
    await database.cursoDao.insertCurso(
      Curso(
          nombreCurso: 'Historia',
          descripcion: 'Descubre la historia universal'),
    );
    await database.cursoDao.insertCurso(
      Curso(nombreCurso: 'Inglés', descripcion: 'Aprende inglés desde cero'),
    );
  }

  runApp(EduApp(database: database));
}

// ===========================
// APP PRINCIPAL
// ===========================

class EduApp extends StatelessWidget {
  final AppDatabase database;
  EduApp({required this.database});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'EduApp',
      debugShowCheckedModeBanner: false,
      initialRoute: '/',
      routes: {
        '/': (context) => InicioPage(),
        '/login': (context) => LoginPage(database: database),
        '/registro': (context) => RegistroPage(database: database),
        '/perfil': (context) => PerfilPage(),
        '/cursos': (context) => CursosPage(database: database),
      },
    );
  }
}

// ===========================
// INICIO
// ===========================

class InicioPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Inicio')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('Bienvenido a EduApp', style: TextStyle(fontSize: 24)),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () => Navigator.pushNamed(context, '/login'),
              child: Text('Iniciar Sesión'),
            ),
            ElevatedButton(
              onPressed: () => Navigator.pushNamed(context, '/registro'),
              child: Text('Registrarse'),
            ),
            ElevatedButton(
              onPressed: () => Navigator.pushNamed(context, '/cursos'),
              child: Text('Ver Cursos'),
            ),
          ],
        ),
      ),
    );
  }
}

// ===========================
// LOGIN
// ===========================

class LoginPage extends StatefulWidget {
  final AppDatabase database;
  LoginPage({required this.database});

  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  final _correoCtrl = TextEditingController();
  final _passCtrl = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Iniciar Sesión')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _correoCtrl,
              decoration: InputDecoration(labelText: 'Correo electrónico'),
            ),
            TextField(
              controller: _passCtrl,
              decoration: InputDecoration(labelText: 'Contraseña'),
              obscureText: true,
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () async {
                final user = await widget.database.usuarioDao
                    .findByCorreo(_correoCtrl.text);

                if (user != null && user.contrasena == _passCtrl.text) {
                  Navigator.pushNamed(context, '/perfil');
                } else {
                  ScaffoldMessenger.of(context).showSnackBar(
                    SnackBar(content: Text('Credenciales incorrectas')),
                  );
                }
              },
              child: Text('Entrar'),
            ),
          ],
        ),
      ),
    );
  }
}

// ===========================
// REGISTRO
// ===========================

class RegistroPage extends StatelessWidget {
  final AppDatabase database;
  final _formKey = GlobalKey<FormState>();
  final _nombreCtrl = TextEditingController();
  final _correoCtrl = TextEditingController();
  final _passCtrl = TextEditingController();

  RegistroPage({required this.database});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Registro de Usuario')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Form(
          key: _formKey,
          child: ListView(
            children: [
              TextFormField(
                controller: _nombreCtrl,
                decoration: InputDecoration(labelText: 'Nombre completo'),
              ),
              TextFormField(
                controller: _correoCtrl,
                decoration: InputDecoration(labelText: 'Correo electrónico'),
              ),
              TextFormField(
                controller: _passCtrl,
                decoration: InputDecoration(labelText: 'Contraseña'),
                obscureText: true,
              ),
              SizedBox(height: 20),
              ElevatedButton(
                onPressed: () async {
                  final usuario = Usuario(
                    nombreCompleto: _nombreCtrl.text,
                    correo: _correoCtrl.text,
                    contrasena: _passCtrl.text,
                  );

                  await database.usuarioDao.insertUsuario(usuario);

                  ScaffoldMessenger.of(context).showSnackBar(
                    SnackBar(content: Text('Usuario registrado correctamente')),
                  );

                  Navigator.pop(context);
                },
                child: Text('Registrar'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

// ===========================
// PERFIL
// ===========================

class PerfilPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Perfil del Usuario')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(Icons.person, size: 100, color: Colors.blueAccent),
            Text('Usuario: Juan Pérez', style: TextStyle(fontSize: 20)),
            Text('Correo: juanperez@gmail.com'),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () => Navigator.pop(context),
              child: Text('Cerrar sesión'),
            ),
          ],
        ),
      ),
    );
  }
}

// ===========================
// CURSOS
// ===========================

class CursosPage extends StatefulWidget {
  final AppDatabase database;
  CursosPage({required this.database});

  @override
  _CursosPageState createState() => _CursosPageState();
}

class _CursosPageState extends State<CursosPage> {
  late Future<List<Curso>> _cursosFuture;

  @override
  void initState() {
    super.initState();
    _cursosFuture = widget.database.cursoDao.findAllCursos();
  }

  Future<void> _inscribirUsuario(int idCurso) async {
    final inscripcion = Inscripcion(
      idUsuario: 1, // Ejemplo
      idCurso: idCurso,
      fechaInscripcion: DateTime.now().toString(),
    );

    await widget.database.inscripcionDao.insertInscripcion(inscripcion);

    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text('Inscripción realizada con éxito')),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Cursos disponibles')),
      body: FutureBuilder<List<Curso>>(
        future: _cursosFuture,
        builder: (context, snapshot) {
          if (!snapshot.hasData)
            return Center(child: CircularProgressIndicator());

          final cursos = snapshot.data!;

          return ListView.builder(
            itemCount: cursos.length,
            itemBuilder: (context, index) {
              final curso = cursos[index];

              return Card(
                margin: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
                child: ListTile(
                  title: Text(curso.nombreCurso),
                  subtitle: Text(curso.descripcion),
                  trailing: ElevatedButton(
                    onPressed: () => _inscribirUsuario(curso.idCurso!),
                    child: Text('Inscribirse'),
                  ),
                ),
              );
            },
          );
        },
      ),
    );
  }
}
