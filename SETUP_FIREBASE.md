# GameCoroFriends — Setup Firebase

## Estructura de archivos
```
GameCoroFriends/
├── index.html                        ← Login Google
└── QuienConoceMas/
    ├── conoces.html                  ← Entrada (requiere login)
    ├── formulario.html               ← Crear formulario (guarda en Firestore)
    └── responder.html                ← Responder por link (carga de Firestore)
```

## Pasos en Firebase Console

### 1. Activar Google Auth
Firebase Console → Authentication → Sign-in method → Google → HABILITAR

### 2. Agregar tu dominio
Firebase Console → Authentication → Settings → Authorized domains
→ Agrega tu dominio (ej: gamecorofriends.web.app o tu dominio custom)

### 3. Crear Firestore Database
Firebase Console → Firestore Database → Crear base de datos
→ Modo: Producción (puedes empezar en modo prueba los primeros 30 días)

### 4. Reglas de Firestore
Firebase Console → Firestore → Reglas → Pega esto:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Formularios: cualquiera puede leer, solo auth puede crear
    match /formularios/{formId} {
      allow read: if true;
      allow create: if request.auth != null;
      allow update: if true; // para incrementar respuestasCount
      allow delete: if request.auth != null && request.auth.uid == resource.data.creadorUid;

      // Resultados: cualquiera puede crear y leer
      match /resultados/{resultId} {
        allow read, create: if true;
      }
    }
  }
}
```

## Estructura de datos en Firestore

### Colección: formularios
```
formularios/
  {formId}/                       ← ID de 8 chars (ej: "aB3kNmPq")
    id: string
    nombre: string                ← Nombre del creador
    creadorUid: string | null
    creadorNombre: string
    preguntas: Array<{texto, opciones[]}>
    respuestas: Array<number[]>   ← Índices de respuestas correctas
    creadoEn: timestamp
    respuestasCount: number

    resultados/                   ← Subcolección
      {autoId}/
        jugador: string
        aciertos: number
        total: number
        porcentaje: number
        fecha: timestamp
```

## El link generado queda así:
```
https://TU-DOMINIO/QuienConoceMas/responder.html?id=aB3kNmPq
```
Limpio, corto y compartible. ✅
