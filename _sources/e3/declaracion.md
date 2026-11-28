# Declaración de autoría, contribuciones y uso de herramientas (Entregable 3)

> **Borrador para completar y firmar.** Las partes entre corchetes las escribe cada autor. Sin las dos
> firmas, esta página no está cerrada.

## Autores

Lina Margarita Buelvas y Camilo Molina Guerrero.

## Contribuciones

Según el historial de git, desde el 2026-11-24 hay 6 commits de Lina y 24 de Camilo, en ramas
distintas. Lo que sigue es lo que hay en el repositorio, por persona; cada uno lo corrige o lo
completa.

**Lina Margarita Buelvas** (ramas `lina/*`):
- semillas 9 y 10 de los 15 modelos, y el análisis del E2 repetido con diez semillas
  (`resultados/semillas_e3/`);
- conjuntos adicionales: descarga, selección, preregistro, adaptador y corridas
  (`src/conjuntos_adicionales.py`, `src/panel_adicional.py`, `src/corridas_adicionales.py`), y
  resultados de ILINet;
- las corridas en GPU de Colab de la familia y de los conjuntos adicionales;
- [Lina: completar].

**Camilo Molina Guerrero** (ramas `camilo/*`, PR #20 a #33):
- estándar, protocolo y análisis de potencia del E3 (`experiments/`);
- modelo original, demostración de la cota de Lipschitz y script de corridas
  (`src/modelo_original.py`, `experiments/lipschitz.md`, `src/original_e3.py`);
- análisis confirmatorio con la regla R3 (`estadistica/ejecutar_e3.py`);
- exportación de los 16 modelos a ONNX, con costo y cuantización (`src/exportar*.py`);
- incertidumbre y Pareto (`estadistica/incertidumbre_e3.py`);
- dashboard (`app.py`, `pages/`, `dashboard/`);
- infraestructura (CI, Docker, licencia, CITATION y referencias);
- fichas, tabla maestra, bloque A y bloque B, y capítulo del E3 en el libro;
- desarrollo del modelo original corrido en su computador;
- [Camilo: completar].

## Uso de herramientas de inteligencia artificial

**Herramienta.** Claude (Anthropic), en la aplicación Claude Code, la misma que se declaró en los
entregables 1 y 2.

**Lado de Camilo.** En el Entregable 3, la herramienta hizo lo siguiente por encargo de Camilo:
- escribió el código, las pruebas y los documentos de las ramas `camilo/*` (PR #20 a #33);
- corrió las verificaciones y las corridas de desarrollo del modelo original en su computador;
- redactó los correos de coordinación con Lina enviados desde su cuenta.

[Camilo: decir qué decidió, qué revisó y qué cambió de lo que hizo la herramienta.]

**Lado de Lina.** [Lina: describir el uso de herramientas de IA en su parte.]

**Lo que no hizo ninguna herramienta.** [Completar entre los dos; por ejemplo, la elección del
problema, las decisiones registradas en `DECISIONES.md` y la sustentación.]

## Firmas

| Autor | Firma | Fecha |
| --- | --- | --- |
| Lina Margarita Buelvas | | |
| Camilo Molina Guerrero | | |
