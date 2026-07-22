# Configuración de Karma y Jasmine en este Proyecto Angular

A partir de las versiones recientes de Angular (15+), el equipo de Angular simplificó significativamente la forma en la que se configuran las pruebas unitarias. Anteriormente era común ver un archivo explícito llamado `karma.conf.js` en la raíz del proyecto. Sin embargo, en tu proyecto, Angular maneja esto de manera interna a través de su **Builder**, ocultando el archivo para mantener la estructura más limpia.

Aquí te explico exactamente de dónde sale la configuración y cómo Angular sabe cómo ejecutar `npm test`.

## 1. El orquestador: `angular.json`
Si abres el archivo `angular.json` en la raíz y buscas la propiedad `"architect"`, encontrarás un nodo llamado `"test"`.

```json
"test": {
  "builder": "@angular-devkit/build-angular:karma",
  "options": {
    "polyfills": [
      "zone.js",
      "zone.js/testing"
    ],
    "tsConfig": "tsconfig.spec.json",
    "inlineStyleLanguage": "scss",
    "assets": [
      "src/favicon.ico",
      "src/assets"
    ],
    "styles": [
      "src/styles.scss"
    ],
    "scripts": []
  }
}
```

**¿Qué significa esto?**
- **builder**: Angular delega toda la configuración a `@angular-devkit/build-angular:karma`. Este paquete ya trae un `karma.conf.js` integrado por defecto que le dice al navegador (usualmente Chrome) cómo debe levantar, compilar el código y generar reportes.
- **polyfills**: Incluye `zone.js/testing`, el cual es el "motor" mágico de Angular que permite utilizar funciones como `fakeAsync` y simular el paso del tiempo.
- **tsConfig**: Le dice a Karma qué reglas de TypeScript debe seguir usando el archivo `tsconfig.spec.json`.

## 2. Las reglas de TypeScript: `tsconfig.spec.json`
Este archivo es crucial. Mientras que `tsconfig.app.json` construye tu aplicación principal, `tsconfig.spec.json` compila específicamente tus archivos `.spec.ts`:

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/spec",
    "types": [
      "jasmine"
    ]
  },
  "include": [
    "src/**/*.spec.ts",
    "src/**/*.d.ts"
  ]
}
```
Aquí radica la razón por la que **solo puedes usar sintaxis de Jasmine** (como `spyOn`). La propiedad `"types": ["jasmine"]` le dice al editor (VS Code) y al compilador que los objetos globales disponibles durante el testeo pertenecen a Jasmine y no a Jest ni a Mocha.

## 3. Dependencias base: `package.json`
Finalmente, cuando ejecutas `npm test`, el script dispara el comando nativo `ng test`. Para que todo funcione, tu proyecto tiene instaladas las siguientes dependencias clave en el bloque de `devDependencies`:

- `"karma"`: El motor de ejecución (test runner) que abre el navegador.
- `"jasmine-core"`: El framework que provee las funciones matemáticas (el `describe`, `it`, `expect`).
- `"karma-jasmine"`: El adaptador o puente que permite a Karma entender los comandos de Jasmine.
- `"karma-chrome-launcher"`: El plugin que Karma utiliza para abrir Google Chrome y correr las pruebas simulando un usuario real.

---

### ¿Y si necesito modificar la configuración de Karma? (Ejemplo: CI/CD o Jenkins)
Si alguna vez necesitas agregar un reportero (como SonarQube), o forzar a que Chrome corra en modo "Headless" (sin abrir ventana visual para integraciones continuas), puedes extraer el archivo de configuración manualmente.

Para ello, basta con:
1. Crear un archivo `karma.conf.js` en la raíz del proyecto.
2. Ir a tu `angular.json` y agregar la propiedad `"karmaConfig": "karma.conf.js"` dentro de `test.options`.

Angular dejará de usar la configuración por defecto y usará tu archivo personalizado. Sin embargo, para fines de aprendizaje local como los que estamos haciendo, ¡la configuración abstracta actual es más que suficiente y perfecta!
