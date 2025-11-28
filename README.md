# Todoticket Calculator

Calculadora de comisiones para retiros de Todoticket. Esta aplicación web permite calcular el retiro óptimo para minimizar el saldo restante en tu cuenta después de aplicar la comisión del 0.6%.

## 🚀 Características

- ✅ Calculadora de retiro óptimo con fórmulas precisas
- ✅ Diseño responsive para móviles y desktop
- ✅ Modo oscuro/claro con detección automática del dispositivo
- ✅ Paleta de colores oficial de Todoticket
- ✅ Interfaz moderna y limpia

## 📋 Requisitos Previos

- Node.js 18+ y npm

## 🛠️ Instalación

1. Instala las dependencias:
```bash
npm install --legacy-peer-deps
```

**Nota:** Se requiere `--legacy-peer-deps` debido a un conflicto de versiones entre TailwindCSS v4 y @astrojs/tailwind que requiere v3. Esto se resolverá cuando @astrojs/tailwind soporte TailwindCSS v4.

## 🧞 Comandos

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `npm run dev`             | Inicia el servidor de desarrollo en `localhost:4321` |
| `npm run build`           | Construye el sitio para producción en `./dist/`  |
| `npm run preview`         | Previsualiza el build localmente                 |

## 📐 Fórmulas Utilizadas

- **Retiro Óptimo**: `REDONDEAR.MENOS(total / 1.006; 2)`
- **Comisión**: `REDONDEAR(retiroOptimo * 0.006; 2)`
- **Restante**: `total - (comision + retiroOptimo)`

Si el restante es mayor a 0, se agrega automáticamente al retiro óptimo para minimizar el saldo restante.

## 🚀 Despliegue en GitHub Pages

La aplicación está configurada para desplegarse automáticamente en GitHub Pages cuando se hace push a la rama `main`. El workflow de GitHub Actions se encuentra en `.github/workflows/deploy.yml`.

### Configuración del Repositorio

1. Ve a Settings > Pages en tu repositorio de GitHub
2. Selecciona "GitHub Actions" como fuente de despliegue
3. El dominio configurado es `todoticket.notakrista.com`

## 🎨 Paleta de Colores

- **Naranja**: `#FF6B35`
- **Azul**: `#004E89`
- **Rojo**: `#E63946`
- **Verde**: `#06A77D`
- **Blanco**: `#FFFFFF`

## 📱 Responsive Design

La aplicación está completamente optimizada para dispositivos móviles, tablets y desktop, utilizando TailwindCSS con un enfoque mobile-first.

## 🌓 Modo Oscuro/Claro

La aplicación detecta automáticamente la preferencia del sistema operativo y permite cambiar manualmente entre modo oscuro y claro. La preferencia se guarda en localStorage.
