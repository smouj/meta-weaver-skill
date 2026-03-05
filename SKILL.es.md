name: meta-weaver
version: 2.4.1
description: Tejedor de sistemas de diseño impulsado por IA para generación automatizada de componentes UI y extracción de tokens de diseño de archivos Figma/Sketch
author: Design Systems Team <design-systems@company.com>
tags: [design, ai, automation]
dependencies:
  - python>=3.9
  - figma-api>=2.0.0
  - pillow>=9.0.0
  - svgwrite>=1.4.0
  - openai>=4.0.0
  - watchdog>=3.0.0
entrypoint: metaweaver
minimum_claw_version: "1.8.0"
capabilities:
  - component-generation
  - token-extraction
  - layout-automation
  - accessibility-validation
  - design-system-sync
```

# Meta Weaver

Tejedor de sistemas de diseño impulsado por IA que automatiza la generación de componentes UI, extrae tokens de diseño y sincroniza sistemas de diseño entre Figma, repositorios de código y documentación de diseño.

## Propósito

Meta Weaver sirve como un puente inteligente entre herramientas de diseño y bases de código. Casos de uso reales incluyen:

- **Generación automatizada de componentes**: Convertir componentes de Figma en componentes React/Vue listos para producción con tipos TypeScript, styled-components y atributos de accesibilidad
- **Extracción de tokens de diseño**: Analizar estilos de Figma (colores, tipografía, espaciado, sombras) y generar CSS custom properties, mapas SCSS u objetos JavaScript para sistemas de diseño
- **Síntesis de diseño responsive**: Generar código CSS de diseño responsive a partir de frames de diseño con ajustes específicos por breakpoint
- **Automatización de modo oscuro**: Crear variantes automáticas de modo oscuro basadas en diseños de modo claro usando transformaciones de color impulsadas por IA
- **Auditoría de accesibilidad**: Validar ratios de contraste, estructura HTML semántica y atributos ARIA contra estándares WCAG 2.1
- **Sincronización de sistema de diseño**: Sincronización bidireccional entre librerías de Figma y librerías de componentes de código con detección y resolución de conflictos
- **Generación de variantes de componentes**: Generar automáticamente todas las variantes de estado (hover, active, disabled, focus) a partir de diseños de componentes base
- **Aplicación de escala de espaciado**: Extraer valores de espaciado consistentes y generar clases de utilidad (margin/padding) siguiendo grids de 4px u 8px

## Alcance

Meta Weaver proporciona estos comandos principales:

### Generación de Componentes

- `metaweaver generate component <figma-file-key> <node-id> --output <path> --framework <react|vue|svelte|angular> --style <css|styled-components|emotion|tailwind> --typescript --include-tests --accessibility-audit`
- `metaweaver generate variants <component-name> --count <n> --pattern <seed> --output <dir>`
- `metaweaver generate layout <frame-id> --breakpoints <xs,sm,md,lg,xl> --container-queries --output <css-file>`
- `metaweaver generate iconset <icon-library-key> --format <svg|font|react-component> --output <dir> --optimize`

### Extracción de Tokens de Diseño

- `metaweaver extract tokens <figma-file-key> --type <colors|typography|spacing|shadows|all> --format <css-variables|scss-maps|json|tokens-json> --output <dir> --prefix <string>`
- `metaweaver analyze-styles <file-path> --detect-overrides --report <format>`
- `metaweaver validate-tokens <token-file> --against <spec-file> --strict`

### Sincronización de Sistema de Diseño

- `metaweaver sync figma-to-code <figma-team> <project-key> --branch <branch-name> --commit-msg <message> --dry-run`
- `metaweaver sync code-to-figma <component-dir> --team-id <id> --library-name <name> --publish`
- `metaweaver diff <source> <target> --format <json|markdown> --output <diff-file>`
- `metaweaver resolve-conflicts <conflict-file> --strategy <prefer-source|prefer-target|merge> --interactive`

### Procesamiento de Imágenes y Assets

- `metaweaver optimize-images <asset-dir> --formats <webp,avif> --quality <0-100> --sizes <1x,2x> --output <optimized-dir>`
- `metaweaver generate-sprite <svg-dir> --output <sprite.svg> --symbol-prefix <icon->`
- `metaweaver convert-svg-to-component <svg-file> --framework <react|vue> --props <size,color> --output <component-file>`

### Validación y QA

- `metaweaver audit <design-file> --checks <contrast,keyboard-nav,semantic-html> --severity <warning|error> --output <report>`
- `metaweaver check-consistency <component-dir> --against <token-file> --fix`
- `metaweaver lint <style-file> --rules <naming,ordering,complexity>`

### Preview y Desarrollo

- `metaweaver preview <component-name> --port <port> --hot-reload --tokens <token-file>`
- `metaweaver serve-docs <component-dir> --output <docs-dir> --theme <light|dark>`
- `metaweaver storybook-generate <component-dir> --framework <react|vue> --output <.storybook>`

## Proceso de Trabajo

### Flujo de Trabajo Típico de Generación de Componentes

1. **Autenticación y Configuración**
   ```
   export FIGMA_API_TOKEN="your_figma_token"
   export OPENAI_API_KEY="your_openai_key"  # optional, for AI enhancements
   metaweaver auth figma --token $FIGMA_API_TOKEN
   ```

2. **Extracción de Tokens** (si se usan tokens de diseño)
   ```
   metaweaver extract tokens "FigmaFileKey" --type all --format css-variables --output ./styles/tokens --prefix --ds
   ```
   La IA analiza relaciones de color para sugerir nombres semánticos (ej., `--ds-color-primary-500` en lugar de `--ds-color-blue-500`).

3. **Generación de Componentes**
   ```
   metaweaver generate component "FigmaFileKey" "nodeId:123:456" \
     --output ./src/components/Button \
     --framework react \
     --style styled-components \
     --typescript \
     --include-tests \
     --accessibility-audit \
     --use-tokens ./styles/tokens/colors.css
   ```
   - Obtiene árbol de componentes de Figma
   - Extrae layout, colores, tipografía
   - Mapea a CSS/styled-components
   - Genera interfaces TypeScript
   - Crea archivos de prueba con RTL/Jest
   - Ejecuta validación de accesibilidad

4. **Validación**
   ```
   metaweaver audit ./src/components/Button/Button.tsx --checks contrast,semantic-html --output ./reports/button-audit.json
   ```
   Verifica ratios de contraste de color, asegura que el botón use elemento `<button>` con atributos aria apropiados.

5. **Commit y Sincronización**
   ```
   metaweaver sync code-to-figma ./src/components --team-id "team_123" --library-name "DS-Components" --publish
   ```
   Sube componentes generados como documentación a Figma, vinculando código con diseño.

### Proceso de Extracción de Tokens

1. Obtener estilos de archivo Figma vía API
2. Analizar definiciones de color, tipografía, espaciado, sombras
3. Clustering con IA: agrupar colores similares, detectar escalas primary/secondary/neutral
4. Generar salida en formato especificado:
   - CSS Variables: `--ds-color-primary-500: #3B82F6;`
   - SCSS Maps: `$colors: ("primary": ("500": #3B82F6));`
   - Tokens JSON: `{"color": {"primary": {"500": {"value": "#3B82F6"}}}`
5. Escribir archivos, mantener index.json

### Proceso de Sincronización de Sistema de Diseño

1. `metaweaver diff figma.code --format json` genera reporte de diferencias
2. Detecta:
   - Nuevos componentes en Figma no en código
   - Componentes deprecados en código no en Figma
   - Desalineaciones de propiedades (tipos de props, valores por defecto)
   - Referencias de tokens desincronizadas
3. Resolución interactiva de conflictos o auto-merge basado en `--strategy`
4. Genera componentes actualizados y commit de cambios

## Reglas de Oro

1. **Nunca modificar archivos generados manualmente**: Todos los componentes generados deben estar marcados con comentario `<!-- Generated by Meta Weaver -->`. Los cambios manuales se sobrescribirán en la siguiente generación.

2. **Usar referencias de tokens semánticos**: Siempre referenciar tokens de diseño (`var(--ds-color-primary-500)`) en lugar de valores hardcodeados. El sistema lo exige vía `metaweaver check-consistency`.

3. **Aislamiento de componentes**: Cada componente generado debe ser autónomo con sus propios estilos, tipos y pruebas. Sin dependencias implícitas en estilos globales.

4. **Accesibilidad primero**: Componentes generados deben pasar WCAG AA mínimo. El flag `--accessibility-audit` es obligatorio para generación de componentes. Fallar builds en violaciones.

5. **Pinning de versiones**: Las versiones de archivo Figma deben estar fijadas. Usar `--figma-version <node-version>` para bloquear a versión de diseño específica. No generar desde "latest" sin restricción de versión en producción.

6. **Jerarquía de tokens**: Los tokens de diseño siguen jerarquía estricta: `category` > `subcategory` > `property` > `value`. Ejemplo: `color.brand.primary.500`. Nunca aplanar.

7. **Consistencia de framework**: Todos los componentes en un proyecto deben usar el mismo framework y enfoque de estilos. Configurar en `metaweaver.config.js` y exigir via pre-commit hook.

8. **Preservar personalizaciones**: Si un archivo de componente contiene región `<!-- METAWEAVER_CUSTOM -->, preservar esa región durante regeneración. Usar para lógica personalizada que no puede autogenerarse.

9. **Siempre generar pruebas**: El flag `--include-tests` es default en modo CI. Generar pruebas para interacciones, variaciones de props y accesibilidad.

10. **Integración con Git**: Los cambios generados deben committearse atómicamente. Usar `metaweaver generate ... && git add . && git commit -m "Generate: <component-name>"`. Nunca committear generaciones parciales.

## Ejemplos

### Ejemplo 1: Generar componente Button desde Figma

```bash
# Usuario ejecuta:
metaweaver generate component "FigmaFileKey" "nodeId:123:456:789" \
  --output src/components/Button \
  --framework react \
  --style styled-components \
  --typescript \
  --include-tests \
  --accessibility-audit \
  --props "size:sm|md|lg,variant:primary|secondary|tertiary"

# Estructura de salida:
src/components/Button/
├── Button.tsx              # Componente principal
├── Button.test.tsx         # Pruebas RTL
├── Button.stories.tsx      # Storybook stories
├── Button.module.css       # Si se usan CSS modules
├── Button.types.ts         # Interfaces TypeScript
├── Button.accessibility.ts # Utilidades de accesibilidad
└── index.ts               # Barrel export

# Fragmento Button.tsx:
import styled from 'styled-components';
import { useButton, UseButtonProps } from './Button.accessibility';

export interface ButtonProps extends UseButtonProps {
  size?: 'sm' | 'md' | 'lg';
  variant?: 'primary' | 'secondary' | 'tertiary';
  children: React.ReactNode;
}

const StyledButton = styled.button<{ $size: string; $variant: string }>`
  padding: var(--ds-space-${$size === 'sm' ? '2' : $size === 'md' ? '3' : '4'});
  background: var(--ds-color-${$variant === 'primary' ? 'primary' : 'neutral'}-500);
  /* ... más estilos */
`;

export const Button = ({ size = 'md', variant = 'primary', children, ...rest }: ButtonProps) => {
  const buttonProps = useButton(rest);
  return (
    <StyledButton $size={size} $variant={variant} {...buttonProps}>
      {children}
    </StyledButton>
  );
};
```

### Ejemplo 2: Extraer tokens de diseño desde Figma y generar CSS variables

```bash
metaweaver extract tokens "FigmaFileKey" \
  --type colors,typography,spacing \
  --format css-variables \
  --output ./src/styles/design-tokens \
  --prefix --ds

# Archivos generados:
./src/styles/design-tokens/
├── colors.css           # Tokens de color
├── typography.css       # Tamaños de fuente, pesos, alturas de línea
├── spacing.css          # Escala de espaciado (grid 4px)
├── shadows.css          # Tokens de sombras
└── index.css            # Importar todo

# Contenido colors.css:
:root {
  /* Colores semánticos derivados de estilos Figma */
  --ds-color-primary-500: #3B82F6;
  --ds-color-primary-600: #2563EB;
  --ds-color-neutral-50: #F9FAFB;
  --ds-color-neutral-100: #F3F4F6;
  /* ... */
}
```

### Ejemplo 3: Validar accesibilidad de archivo de diseño

```bash
metaweaver audit "FigmaFileKey" "nodeId:123:456" \
  --checks contrast,keyboard-nav,semantic-html \
  --severity error \
  --output ./reports/accessibility.json

# Salida del reporte:
{
  "nodeId": "123:456",
  "name": "Submit Button",
  "violations": [
    {
      "type": "contrast",
      "severity": "error",
      "message": "Color contrast ratio 3.2:1 fails WCAG AA (minimum 4.5:1)",
      "expected": "4.5:1",
      "actual": "3.2:1",
      "fix": "Use background #1D4ED8 instead of #3B82F6"
    }
  ]
}
```

### Ejemplo 4: Optimizar iconos SVG y generar componentes React

```bash
metaweaver convert-svg-to-component ./assets/icons/*.svg \
  --framework react \
  --props "size,color,filled" \
  --output ./src/components/Icons

# Para arrow-left.svg genera:
# IconArrowLeft.tsx con interfaz de props:
interface IconArrowLeftProps {
  size?: number | 'sm' | 'md' | 'lg';
  color?: string;
  filled?: boolean;
}

export const IconArrowLeft = ({ size = 24, color = 'currentColor', filled = false }: IconArrowLeftProps) => (
  <svg width={typeof size === 'number' ? size : sizeMap[size]} height={...} fill={filled ? color : 'none'} stroke={color}>
    <path d="M15 19l-7-7 7-7" />
  </svg>
);
```

### Ejemplo 5: Sincronizar sistema de diseño entre Figma y código

```bash
# Primero dry run para ver cambios
metaweaver sync figma-to-code "team_123" "project_key" \
  --branch feature/sync-design \
  --commit-msg "chore(design): sync components from Figma" \
  --dry-run

# Salida:
# Components to generate: Button, Input, Modal, Card
# Components to deprecate: OldButton
# Tokens to update: colors (12 changes), spacing (4 changes)
# Conflicts detected: 2 (Modal.modalProps - resolve manually)

# Después de resolver conflictos, ejecutar sync real:
metaweaver sync figma-to-code "team_123" "project_key" \
  --branch feature/sync-design \
  --commit-msg "chore(design): sync components from Figma" \
  --interactive-conflicts

# Genera componentes, actualiza archivos de tokens, crea commit con todos los cambios.
```

### Ejemplo 6: Generar CSS de layout responsive desde frame de Figma

```bash
metaweaver generate layout "nodeId:123:789" \
  --breakpoints xs,sm,md,lg,xl \
  --container-queries \
  --output ./src/styles/layouts/grid.css

# Salida incluye:
# - CSS mobile-first con clamp() para espaciado fluido
# - Soporte de container queries para @container
# - CSS custom properties para tokens de layout
# - Overrides de breakpoint con media queries min-width
```

## Comandos de Rollback

Meta Weaver proporciona operaciones específicas de rollback:

### Rollback de generación de componentes

```bash
# Antes de generar, Meta Weaver crea backup en .metaweaver/backups/
metaweaver generate component "figma-key" "node-id" --output ./src/components

# Si la generación produce código roto:
metaweaver rollback component ./src/components/Button --to .metaweaver/backups/2026-03-05T14:30:00Z

# O restaurar desde último backup automático:
metaweaver restore-backup ./src/components/Button
```

### Rollback de cambios de tokens de diseño

```bash
# Listar snapshots de tokens disponibles:
metaweaver token-snapshots list --output json

# Restaurar tokens a estado anterior:
metaweaver token-snapshots restore --date "2026-03-04" --type colors,spacing
```

### Deshacer operaciones de sync

```bash
metaweaver sync figma-to-code team project --dry-run
# Se da cuenta que son muchos cambios

# Abortar el sync (solo funciona antes del commit):
metaweaver sync cancel

# Si ya se commitió, revertir git commit creado por Meta Weaver:
git revert HEAD --no-edit
# Meta Weaver tag asegura que revert solo afecte sus cambios:
# git revert --grep "chore(design): sync components from Figma"
```

### Resetear estado local

```bash
# Limpiar cache de Meta Weaver y archivos temporales:
metaweaver cache clear --all

# Re-autenticar si tokens corruptos:
metaweaver auth logout figma && metaweaver auth figma --token $NEW_TOKEN
```

### Rollback de resolución de conflictos

```bash
# Si la resolución de conflictos produjo merge malo:
metaweaver conflicts rollback --strategy restore-backup --backup .metaweaver/conflict-backup-2026-03-05T14:25:00Z
```

## Dependencias y Requisitos

- **Figma API Token**: Requerido para acceder a archivos Figma. Configurar via variable de entorno `FIGMA_API_TOKEN` o `metaweaver auth figma`.
- **Node.js 18+**: Para ejecutar código de componentes generados y preview de Storybook.
- **Python 3.9+**: Requerido para el CLI de Meta Weaver mismo.
- **Opcional - OpenAI API**: Para nombrado semántico de tokens impulsado por IA y detección de patrones de diseño. Configurar `OPENAI_API_KEY`.
- **Git**: Requerido para operaciones de sync y tracking de rollback.

### Dependencias del Sistema (instalar via `metaweaver doctor`)

```bash
# Verificar todas las dependencias:
metaweaver doctor

# Arreglar problemas comunes:
metaweaver doctor --fix
```

### Requisitos de Memoria

- Mínimo 4GB RAM para extracción de tokens en archivos grandes
- 8GB+ recomendado para generación de componentes con mejoras de IA
- 512MB espacio en disco mínimo para caches y backups

## Pasos de Verificación

Después de cualquier operación de generación o sync, ejecutar:

```bash
# 1. Verificación de tipos (si TypeScript)
metaweaver typecheck ./src/components/GeneratedComponent

# 2. Validación de accesibilidad
metaweaver audit ./src/components/GeneratedComponent --severity error

# 3. Verificación de consistencia contra tokens
metaweaver check-consistency ./src/components --against ./src/styles/design-tokens --fix

# 4. Ejecutar pruebas de componentes
npm test -- GeneratedComponent

# 5. Test de regresión visual (opcional)
if [ -f ".metaweaver/config.yml" ] && grep -q "visualTesting: true" .metaweaver/config.yml; then
  metaweaver visual-test ./src/components/GeneratedComponent --baseline ./baseline-screenshots
fi
```

Todas las verificaciones deben pasar antes de commitear código generado. CI gate:

```yaml
# .github/workflows/design-sync.yml
- name: Verify generated components
  run: |
    metaweaver typecheck ./src/components
    metaweaver audit ./src/components --severity error --output ./audit-report.json
    test -s ./audit-report.json && echo "No violations found" || (cat ./audit-report.json && exit 1)
```

## Solución de Problemas

### Límites de tasa de API de Figma
```
Error: FIGMA_API_RATE_LIMIT_EXCEEDED
```
Solución: Agregar `--rate-limit 60` para generar máximo 60 requests/minuto, o dividir archivo grande en lotes más pequeños.

### Extracción de tokens falla en estilos de color personalizados
```
Error: Unsupported color format: "gradient-linear-90"
```
Solución: Gradientes de Figma no soportados para tokens. Convertir a colores sólidos o excluir via `--skip-types gradients`.

### Componentes generados tienen espaciado incorrecto
```
Síntoma: Tokens no aplicados, valores de píxeles hardcodeados aparecen
```
Causa: Falta flag `use-tokens` o ruta de archivo de tokens incorrecta.
Solución: Asegurar que archivo de tokens está importado en componente y valores usan sintaxis `var(--ds-space-*)`.

### Timeouts de mejoras IA
```
Error: OPENAI_TIMEOUT after 30000ms
```
Solución: Configurar `OPENAI_TIMEOUT=60000` o deshabilitar IA con `--no-ai` para generación más rápida sin nombrado semántico.

### Conflictos de sync en merge 3-Way
```
Error: Conflict in Button.tsx: PROPERTIES_MISMATCH
```
Solución: Usar `metaweaver resolve-conflicts ./conflict.json --interactive` para resolver manualmente cada conflicto, eligiendo un lado o mergeando.

### Servidor de preview falla al iniciar
```
Error: Port 3000 already in use
```
Solución: Especificar puerto diferente: `metaweaver preview Button --port 3001`

### Componente generado falla auditoría de accesibilidad
```
Violation: Button element missing accessible name
```
Solución: Asegurar `aria-label` o contenido de texto visible. Si botón solo icono, agregar `--include-label "Submit"` al comando de generación.

### Rendimiento: Extracción de tokens lenta en archivos grandes
Solución: Usar extracción incremental: `metaweaver extract tokens --modified-since "2026-03-01"` o apuntar tipos de estilo específicos: `--type colors,typography`.

### Problemas de paths en Windows
```
Error: Path too long for Windows
```
Solución: Habilitar paths largos en registro de Windows o output a paths más cortos. Usar forward slashes: `--output ./c/design-system`.

### Conflictos de merge Git en archivos generados
```
<<<<<<< HEAD
// Generated by Meta Weaver v2.4.1
=======
// Generated by Meta Weaver v2.4.2
>>>>>>> branch
```
Solución: Siempre regenerar componente en lugar de merge manual. Ejecutar `metaweaver generate component ...` para sobrescribir ambos lados con última versión.
```