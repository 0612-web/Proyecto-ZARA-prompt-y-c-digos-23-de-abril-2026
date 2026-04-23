¡Hola! Como desarrollador senior, entiendo perfectamente el enfoque. Vamos a estructurar el proyecto **Crudzara** bajo la carpeta `xfluttervania0612/crudzara`. Implementaremos una arquitectura robusta, integrando Firebase y la estética solicitada (Café, Negro y Blanco) para que sea un material educativo de alto nivel.

---

## 1. Configuración de Entorno y Estructura
Primero, creamos la jerarquía de carpetas y el proyecto.

```bash
mkdir xfluttervania0612
cd xfluttervania0612
flutter create crudzara
cd crudzara
```

### Metodología de Agentes y Roles (Workflow)
Para este proyecto, definiremos los siguientes roles para el desarrollo:
* **Arquitecto de Datos:** Configura Firestore y el modelo de datos.
* **Ingeniero de Integración:** Gestiona `firebase_core` y dependencias.
* **Desarrollador UI/UX:** Implementa el diseño "Elegant Coffee" (Café, Blanco, Negro).
* **Lógica de Negocio (CRUD):** Desarrolla los métodos de flujo de datos.

---

## 2. Configuración de Firebase y Librerías

### Librerías necesarias (pubspec.yaml)
Para integrar Firebase y las funcionalidades de diseño, tu archivo `pubspec.yaml` debe quedar así:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.2
  cloud_firestore: ^4.14.0
  google_fonts: ^6.1.0  # Para la tipografía armoniosa
```

**Instalación:** Ejecuta `flutter pub get` en la terminal.

### Configuración en Consola Firebase
1.  Ve a [Firebase Console](https://console.firebase.google.com/).
2.  Crea un proyecto llamado **Crudzara**.
3.  En el menú lateral, ve a **Firestore Database** y haz clic en **Crear base de datos**.
4.  Crea una colección llamada `camisas`.
5.  Registra tu app (Android/iOS) y descarga el archivo `google-services.json` en `android/app/`.

---

## 3. Desarrollo de la Práctica Guiada (Antigravity & CRUD)

Para trabajar con un concepto de "antigravity" (mantener el código ligero, fluido y desacoplado), separaremos el modelo de la interfaz.

### Estructura de Archivos
* `lib/models/camisa_model.dart`
* `lib/services/firebase_service.dart`
* `lib/screens/home_screen.dart`
* `lib/main.dart`

### A. Modelo de Datos (`camisa_model.dart`)
```dart
class Camisa {
  String id;
  String nombre;
  double precio;
  int stock;

  Camisa({required this.id, required this.nombre, required this.precio, required this.stock});

  Map<String, dynamic> toMap() {
    return {'nombre': nombre, 'precio': precio, 'stock': stock};
  }
}
```

### B. Lógica CRUD (`firebase_service.dart`)


```dart
import 'package:cloud_firestore/cloud_firestore.dart';

FirebaseFirestore db = FirebaseFirestore.instance;

// CREATE
Future<void> addCamisa(String nombre, double precio, int stock) async {
  await db.collection('camisas').add({
    'nombre': nombre,
    'precio': precio,
    'stock': stock,
  });
}

// READ
Stream<QuerySnapshot> getCamisas() {
  return db.collection('camisas').snapshots();
}

// UPDATE
Future<void> updateCamisa(String id, String n, double p, int s) async {
  await db.collection('camisas').doc(id).update({
    'nombre': n, 'precio': p, 'stock': s
  });
}

// DELETE
Future<void> deleteCamisa(String id) async {
  await db.collection('camisas').doc(id).delete();
}
```

---

## 4. Interfaz "Elegant Coffee" (`home_screen.dart`)

Utilizaremos una paleta de colores: 
* **Café:** `Color(0xFF4E342E)` 
* **Negro:** `Color(0xFF212121)`
* **Blanco:** `Color(0xFFFAFAFA)`

```dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import '../services/firebase_service.dart';

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xFFFAFAFA),
      appBar: AppBar(
        title: Text('CRUDZARA - Premium', style: Google_fonts.playfairDisplay(color: Colors.white)),
        backgroundColor: Color(0xFF212121),
      ),
      body: StreamBuilder(
        stream: getCamisas(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          var docs = snapshot.data!.docs;
          return ListView.builder(
            itemCount: docs.length,
            itemBuilder: (context, i) {
              return Card(
                color: Color(0xFF4E342E),
                margin: EdgeInsets.symmetric(horizontal: 15, vertical: 8),
                child: ListTile(
                  title: Text(docs[i]['nombre'], style: TextStyle(color: Colors.white, fontWeight: FontWeight.bold)),
                  subtitle: Text("Precio: \$${docs[i]['precio']} - Stock: ${docs[i]['stock']}", 
                               style: TextStyle(color: Colors.white70)),
                  trailing: IconButton(
                    icon: Icon(Icons.delete, color: Colors.white),
                    onPressed: () => deleteCamisa(docs[i].id),
                  ),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Color(0xFF4E342E),
        child: Icon(Icons.add, color: Colors.white),
        onPressed: () => _showAddDialog(context),
      ),
    );
  }

  void _showAddDialog(BuildContext context) {
    // Aquí se implementaría el formulario con los 3 campos solicitados
  }
}
```

---

## 5. Inicialización (`main.dart`)

```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'screens/home_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Inicialización de Firebase
  runApp(CrudzaraApp());
}

class CrudzaraApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Crudzara',
      theme: ThemeData(
        brightness: Brightness.light,
        primaryColor: Color(0xFF212121),
      ),
      home: HomeScreen(),
    );
  }
}
```

### Resumen de la Práctica para Estudiantes:
1.  **Agentes:** Cada estudiante asume un rol (UI, Lógica o Base de Datos).
2.  **Skills:** Dominio de `StreamBuilder` para tiempo real.
3.  **Flujo:** El cambio en la consola de Firebase se refleja instantáneamente en la app gracias a la "antigravedad" (el estado no pesa, fluye desde la nube).
