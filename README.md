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
