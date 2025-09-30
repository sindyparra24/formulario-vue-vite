# Formulario Web - Proyecto 3 (Vue + Vite)

##  Descripción
Este proyecto corresponde al tercer módulo del despliegue básico de proyectos.  
Consiste en un **formulario desarrollado con Vue 3 y Vite**, conectado al backend del Proyecto 1 (Node + Express + MongoDB Atlas).  
Se implementó un CRUD completo con validaciones y estilo inspirado en la temática Marvel.

##  Objetivos
- Desarrollar un formulario dinámico con Vue y Vite.
- Conectar el frontend al backend mediante API REST.
- Implementar operaciones CRUD: crear, leer, actualizar y eliminar registros.
- Validar campos (DNI, nombres, apellidos, fecha de nacimiento, género, ciudad).
- Aplicar un estilo visual atractivo basado en Marvel.

##  Estructura del Proyecto
formulario-vue-vite/
│
├── public/ # Archivos públicos
├── src/ # Código fuente principal
│ ├── App.vue # Componente principal con formulario + tabla
│ ├── main.js # Entrada de la aplicación
│ └── style.css # Estilos globales (tema Marvel)
│
├── .env # Variables de entorno (URL API backend)
├── package.json # Dependencias y scripts
├── vite.config.js # Configuración de Vite
└── README.md # Documentación del proyecto


##  Configuración

Ejecutar en desarrollo

npm run dev
Abrir en el navegador la URL que indique la consola (ej. http://localhost:5173).

Pruebas CRUD
Ejemplo de datos válidos para registrar:

DNI: 0987654323

Nombres: Camila Andrea

Apellidos: Serrano López

Fecha Nacimiento: 1996-08-20

Género: F

Ciudad: Quito

Autora
Proyecto desarrollado por Sindy Parra
