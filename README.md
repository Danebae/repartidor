# Repartidor · Gastos de la cuadrilla

Aplicación web para repartir los gastos de una comida, excursión, barbacoa o fin de semana entre unidades familiares. Calcula cuánto corresponde a cada familia, quién debe pagar o recibir y las transferencias concretas para saldar las cuentas.

**Solo necesitas `index.html` y un navegador moderno.** Funciona con doble clic, sin instalar nada, sin servidor y sin conexión, incluida la generación de PDF.

## Empezar

1. Descarga el repositorio desde **Code → Download ZIP**, descomprime y abre `index.html` con tu navegador. También puedes clonar por SSH:

   ```sh
   git clone git@github.com:Danebae/repartidor.git
   ```

2. Escribe el nombre de la actividad y, si quieres, su fecha.
3. Pulsa **Añadir familia**. Introduce un nombre, el número de adultos y niños y el dinero que ha adelantado cada familia. Repite para todas las participantes.
4. Elige cómo cuentan los niños: **no pagan**, **pagan la mitad** o **pagan como un adulto**.
5. Pulsa **Calcular reparto** y revisa los balances y **¿Quién paga a quién?**.
6. Descarga el PDF o utiliza **Compartir por WhatsApp**.

El botón azul de información de la esquina superior izquierda abre estas instrucciones en GitHub. Ese enlace necesita conexión; el cálculo y el PDF funcionan sin ella.

## Familias e importes

- Cada fila representa una unidad familiar; puede tener uno o varios adultos, solo niños o cualquier combinación con al menos una persona.
- Las familias que no hayan adelantado dinero también participan. Introduce **0** en sus gastos.
- Puedes añadir y eliminar familias. El borrado exige confirmación.
- Introduce euros como `12,50` o `12.50`, con hasta dos decimales y **sin separadores de miles ni símbolo €**. No se aceptan importes negativos, valores vacíos o formatos ambiguos.
- Los adultos y niños son enteros no negativos. Cada familia necesita un nombre. No hay un límite de familias impuesto por la aplicación.
- El nombre de actividad es opcional; si queda vacío, el informe utiliza «Actividad de la cuadrilla».

## Cómo se calcula

El peso de cada familia es `adultos + niños × factor`, con factor 0, 0,5 o 1. El gasto se reparte proporcionalmente a esos pesos.

Los importes y operaciones monetarias se calculan con **céntimos enteros y BigInt**. Primero se trunca la cuota de cada familia; después se reparten los céntimos sobrantes por el método de los mayores restos. Los empates se resuelven por orden de aparición. Las cuotas suman exactamente el gasto total y los saldos suman cero.

Una familia con peso cero no paga cuota y recupera íntegramente cualquier dinero adelantado. Si todos los pesos son cero y hay gastos, la aplicación pide cambiar los participantes o el criterio. Con gasto cero no genera deudas ni transferencias.

Las transferencias emparejan los mayores deudores y acreedores de forma determinista. Se comprueba que cada familia pague y reciba exactamente su saldo; no se busca la minimización matemática absoluta de pagos.

El importe individual y el gasto total deben caber en **9.007.199.254.740.991 céntimos**. El coste por unidad mostrado es informativo y aproximado; no se utiliza para recalcular las cuotas.

## PDF y WhatsApp

El PDF incluye la actividad, las fechas, el resumen, los integrantes, los anticipos, las cuotas, los saldos y las transferencias. Se genera como texto y tablas A4, con paginación automática y caracteres españoles. **jsPDF y la fuente están integrados en el HTML:** no se descarga ninguna biblioteca al usarlo.

- **Descargar PDF** guarda el informe para revisarlo, imprimirlo o enviarlo.
- **Compartir por WhatsApp** utiliza el menú del sistema si el navegador permite compartir archivos. Elige WhatsApp y la conversación o grupo. La aplicación no elige destinatarios ni acredita que el envío se haya completado.
- Si el navegador no permite compartir el archivo, se descarga el PDF y se abre **WhatsApp Web**. Selecciona el grupo y adjunta manualmente el documento. Si se bloquea la ventana, usa el enlace que aparece en pantalla.
- **Copiar mensaje** copia un texto de acompañamiento. Si el navegador bloquea el portapapeles, el texto queda seleccionado para copiarlo manualmente.

WhatsApp requiere Internet. Web Share API y el portapapeles dependen del navegador y pueden estar limitados al abrir archivos locales. Su ausencia no impide calcular ni descargar el PDF.

Si cambias cualquier dato después de calcular, el resultado y el informe anterior se invalidan. **Vuelve a calcular antes de descargar o compartir.**

## Guardado y privacidad

Los datos se guardan automáticamente en `localStorage`, con la clave `cuadrilla-gastos-v1`. Al volver a abrir el mismo archivo con el mismo navegador se recuperan, siempre que el navegador conserve su almacenamiento. Los resultados se vuelven a calcular.

- No hay cuentas, cookies, backend, publicidad ni telemetría. Los nombres y gastos no se envían a servidores.
- Compartir un PDF transmite los datos del informe al destino que tú elijas.
- El almacenamiento depende del navegador, su perfil, el origen y, al usar `file://`, de cómo trate la ruta local. **Mover o renombrar el HTML puede hacer que no aparezcan los datos anteriores.** No se sincronizan entre dispositivos.
- El modo privado y borrar los datos del navegador pueden eliminar la información guardada.
- Si el almacenamiento está lleno o bloqueado, aparece un aviso y los datos siguen en memoria durante esa sesión. Conserva tu información antes de cerrar la pestaña.
- **Limpiar todos los datos** abre un modal. Cancelar o pulsar Escape conserva todo; confirmar elimina los datos de esta aplicación y devuelve el criterio de niños a «pagan la mitad».

## Compatibilidad y comprobaciones

Utiliza una versión reciente de Chrome, Edge, Firefox o Safari con JavaScript activado. El reparto requiere soporte de BigInt y los modales usan `dialog`. La interfaz presenta tablas en escritorio y tarjetas editables en móvil.

Comprobado con 19 pruebas de lógica, incluidos los casos límite monetarios y 5.000 repartos aleatorios; 17 recorridos automatizados de Chromium desde `file://` sin conexión; y una comprobación integrada en Firefox. Se han verificado la persistencia, los borrados, los nombres especiales, las alternativas de uso compartido y el bloqueo de informes desactualizados. Un PDF real de 35 familias y seis páginas conserva todos los importes y transferencias de la pantalla. La integración nativa de compartir se prueba mediante simulación con un archivo PDF real; su disponibilidad depende del dispositivo.

## Archivos y licencia

El repositorio publica únicamente `index.html`, este `README.md` y `LICENSE`. El CSS y JavaScript están dentro del HTML. No necesitas Node, npm, compiladores ni archivos auxiliares. Las evidencias y herramientas locales de comprobación quedan fuera de Git.

El código propio se distribuye bajo [licencia MIT](LICENSE), copyright © 2026 Jon Bilbao. Los componentes de terceros conservan sus licencias y avisos integrados en `index.html`:

- [jsPDF 4.2.1](https://github.com/parallax/jsPDF/releases/tag/v4.2.1), licencia MIT.
- [DejaVu Sans](https://dejavu-fonts.github.io/), licencia Bitstream Vera / DejaVu, utilizada para el PDF.
- Icono [Info de Font Awesome Free 7.3.1](https://fontawesome.com/icons/info?f=classic&s=solid), copyright Fonticons, Inc., bajo [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Se integra el SVG oficial con su atribución; no se carga una biblioteca de iconos.
