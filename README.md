# lenguaje-m-editor-avanzado-

## Si en lugar de Text.Proper hubieras usado Text.Upper para estandarizar la categoría, ¿qué tendrías que cambiar en el filtro de los registros de prueba? ¿Por qué?
Si hubiera usado Text.Upper, convertiria todo en mayuscula, por lo tanto deberia cambiar el filtro de los registros de prueba

## Si mañana llega un registro con la categoría "prueba " (con un espacio al final), ¿tu script lo elimina? Si no, ¿qué paso agregarías y en qué lugar del script?
Con el script actual, **no** lo eliminaría. Aunque `Text.Proper` corregiría las mayúsculas/minúsculas, dejaría la categoría como `"Prueba "`, manteniendo el espacio oculto al final. Debido a esto, la comparación estricta `[categoria] <> "Prueba"` evaluará que `"Prueba "` $\neq$ `"Prueba"` y el registro sobrevivirá al filtro.
* **Solución (Qué paso agregar y dónde):** Se debe aplicar una limpieza de espacios (`Text.Trim`) también a la columna `categoria` antes de realizar el filtrado. Lo ideal es incluirla en el **Paso 2** junto con el nombre del producto:
  ```powerquery
  LimpiarEspacios = Table.TransformColumns(#"Changed Type", {
      {"nombre_producto", Text.Trim, type text}, 
      {"categoria", Text.Trim, type text}
  }),

## ¿Cuál es la diferencia entre usar Text.Trim y Text.Clean en M?Text.Trim: Su función principal es eliminar los espacios en blanco (" ") ubicados estrictamente al inicio y al final de una cadena de texto (ideal para corregir errores de tipeo en la carga). No afecta los espacios entre palabras.

Text.Clean: Su propósito es remover caracteres no imprimibles o de control del sistema (como saltos de línea #(lf) o tabulaciones) que a menudo se arrastran al importar datos desde sistemas legacy.

## ¿Por qué filtraste los registros "PRUEBA" después de estandarizar la categoría y no antes?
Si hubiésemos intentado filtrar los registros de prueba antes de estandarizar, el filtro habría tenido que contemplar todas las variaciones posibles de mayúsculas/minúsculas y espacios de forma simultánea (ej. "PRUEBA", "Prueba", "prueba", " PRUEBA ").

Al estandarizar primero con Text.Proper, unificamos el formato de toda la categoría a un estándar predecible ("Prueba"), permitiendo que el paso de filtrado posterior sea limpio, simple, infalible y fácil de mantener.
