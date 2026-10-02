# Informe 1 — Unlearnable Examples, ataques contra ellos y defensas: análisis exhaustivo de los primeros 13 papers

> **Alcance.** Este informe trabaja **exclusivamente** con los 13 primeros artículos del repositorio (orden alfabético de carpetas, tal como aparecen al listar el repositorio). No incorpora información externa: cada dato numérico, fórmula o afirmación proviene del archivo Markdown del paper correspondiente. Cuando un paper menciona a otro trabajo (por ejemplo, una cifra de un baseline), se indica que la cifra la reporta *ese* paper.
>
> El segundo informe (papers 14–26) se hará cuando lo indiques. Nota: el repositorio tiene 27 carpetas porque *armor: Shielding Unlearnable Examples against…* aparece dos veces (dos conversiones del mismo artículo); esa duplicación cae en el segundo bloque y no afecta a este informe.

---

## Índice

- [0. Cómo leer este informe](#0-cómo-leer-este-informe)
- [1. Conceptos fundamentales (glosario explicado)](#1-conceptos-fundamentales-glosario-explicado)
- [2. Resumen completo y exhaustivo de los 13 papers](#2-resumen-completo-y-exhaustivo-de-los-13-papers)
- [3. Informe detallado por paper](#3-informe-detallado-por-paper)
  - [P1 · Ensembles contra unlearnable data](#p1--advancing-ensemble-learning-against-unlearnable-data)
  - [P2 · CUDA](#p2--cuda-convolution-based-unlearnable-datasets)
  - [P3 · CMCR](#p3--cross-modal-collaborative-recovery-cmcr)
  - [P4 · EPD + COIN (+ VUDA/HUDA)](#p4--detecting-and-corrupting-convolution-based-unlearnable-examples-epd--coin)
  - [P5 · Detección (Simple Networks / Bias-shifting) + SDA+AN + teoría de presupuestos](#p5--detection-and-defense-of-unlearnable-examples)
  - [P6 · EUN](#p6--eun-enhanced-unlearnable-examples-generation-approach)
  - [P7 · VESPR](#p7--vespr-exploiting-supervised-poison-vulnerability-to-strengthen-self-supervised-defense)
  - [P8 · GUE](#p8--gue-game-theoretic-unlearnable-example-generator)
  - [P9 · KBS](#p9--kbs-k-space-bispectrum-steganography)
  - [P10 · IRP](#p10--irp-leveraging-imperfect-restoration-for-data-availability-attack)
  - [P11 · Poisoning de clustering](#p11--novel-poisoning-attacks-for-clustering-methods-via-robust-feature-generation)
  - [P12 · PyrFawkes](#p12--pyrfawkes-protecting-personal-privacy-against-unauthorized-facial-recognition-models-via-image-cloaking)
  - [P13 · Reversible Unlearnable Examples](#p13--reversible-unlearnable-examples-towards-the-copyright-protection-in-deep-learning-era)
- [4. Análisis comparativo entre los 13 papers](#4-análisis-comparativo-entre-los-13-papers)
- [5. Casos aplicados transversales](#5-casos-aplicados-transversales)
- [6. Vacíos, inconsistencias detectadas y límites de este informe](#6-vacíos-inconsistencias-detectadas-y-límites-de-este-informe)
- [Anexo A. Archivos fuente](#anexo-a-archivos-fuente)

---

## 0. Cómo leer este informe

### 0.1 Los 13 papers y su identificador

| ID | Título (abreviado) | Rol principal | Lugar de publicación visible en el md |
|---|---|---|---|
| **P1** | *Advancing ensemble learning against unlearnable data* (Hapuarachchi & Xiong) | **Ataque** contra datos protegidos (los vuelve aprendibles) | Neurocomputing (DOI 10.1016/j.neucom.2025.130422) |
| **P2** | *CUDA: Convolution-based Unlearnable DAtaset* (Sadasivan, Soltanolkotabi, Feizi) | **Protección** (genera unlearnable data) | No visible en el md; P1 lo cita como CVPR 2023 |
| **P3** | *Cross-Modal Collaborative Recovery (CMCR)* (Li et al.) | **Ataque** contra protección multimodal | No visible (formato IEEE de conferencia) |
| **P4** | *Detecting and Corrupting Convolution-based Unlearnable Examples* (Li, Wang et al.) | **Detección + defensa** (EPD, COIN) y dos nuevas protecciones (VUDA, HUDA) | AAAI 2025 (línea de copyright) |
| **P5** | *Detection and Defense of Unlearnable Examples* (Zhu, Yu, Gao) | **Detección + defensa + teoría** | AAAI 2024 (línea de copyright) |
| **P6** | *EUN: Enhanced unlearnable examples generation approach* (Chen et al.) | **Protección** | Revista Elsevier (nombre no visible) |
| **P7** | *VESPR* (Styborski, Lyu, Huang, Kong) | **Defensa** (entrenar bien pese al veneno) | No visible (formato LNCS) |
| **P8** | *GUE: Game-Theoretic Unlearnable Example Generator* (Liu, Wang, Gao) | **Protección** con teoría de juegos | No visible en el md; P1 lo cita como AAAI 2024 |
| **P9** | *KBS: K-Space Bispectrum Steganography* (Li et al.) | **Protección** | ACM MM '25 |
| **P10** | *IRP: Imperfect Restoration Poisoning* (Huang, Styborski, Lyu, Wang, Kong) | **Protección** (mismo grupo que P7) | No visible (formato LNCS) |
| **P11** | *Novel poisoning attacks for clustering methods* (Zhang & Tang) | **Ataque/protección** sobre *clustering* | Revista Elsevier (nombre no visible) |
| **P12** | *PyrFawkes: image cloaking contra reconocimiento facial* (Guo, Xu, Xu) | **Protección** de privacidad facial | No visible (formato IEEE; el abstract dice "thesis") |
| **P13** | *Reversible Unlearnable Examples* (Wang, Tian et al.) | **Protección** reversible + marca de agua | IEEE © 2025 (revista no visible) |

> **Ojo con la palabra "ataque".** En esta literatura la palabra cambia de sentido según el punto de vista. Generar *unlearnable examples* es técnicamente un **ataque de envenenamiento** (*availability attack*) contra quien entrena el modelo, pero su propósito es **proteger** al dueño de los datos. A su vez, romper esa protección (P1, P3) es una "defensa" desde el punto de vista del entrenador, pero un **ataque a la privacidad** desde el punto de vista del dueño. En este informe uso: **Protección** = método que vuelve los datos no aprendibles; **Ataque (a la protección)** = método que los vuelve aprendibles otra vez; **Defensa/Detección** = técnica del entrenador para detectar o neutralizar la protección.

### 0.2 Etiquetas epistemológicas (usadas en todo el informe)

Para cumplir con tu pedido de diferenciar siempre el estatus de cada afirmación, uso estas cuatro etiquetas:

- **[Afirma]** — lo que el paper *dice* (en abstract, introducción o conclusiones), aunque no lo pruebe.
- **[Demuestra]** — lo que el paper *muestra con experimentos o con una prueba matemática* incluida en el md (cito tabla/figura/teorema).
- **[Inferencia]** — lo que *yo deduzco* comparando cifras dentro de un paper o entre papers. Es razonamiento mío, no una afirmación de los autores.
- **[No especificado]** — información necesaria para reproducir o interpretar el resultado que el md no proporciona.

### 0.3 Analogías

Las analogías aparecen marcadas con 🔎 **Analogía**. Inmediatamente después siempre viene ✅ **Explicación técnica correcta**, porque toda analogía simplifica y puede inducir a error si se toma literalmente.

---

## 1. Conceptos fundamentales (glosario explicado)

Esta sección explica, **la primera vez que aparecen**, los términos que se repiten en varios papers. Los términos específicos de un único paper se explican dentro de la sección de ese paper.

### 1.1 Unlearnable examples / unlearnable data / unlearnable dataset (ULD)

🔎 **Analogía.** Imagina que alguien fotocopia a escondidas tus apuntes para estudiar. Tú escribes en cada página una "pista" casi invisible (por ejemplo, un punto en una esquina que siempre coincide con la respuesta correcta). El copión aprende a guiarse por los puntos y no por el contenido; cuando llega el examen real (sin puntos), no sabe nada.

✅ **Explicación técnica.** Un *unlearnable example* es una imagen (u otro dato) a la que se añade una modificación pequeña —habitualmente imperceptible— de modo que un modelo de *deep learning* entrenado con esos datos obtenga **baja exactitud sobre datos limpios de prueba** (*clean test accuracy*), aunque su exactitud de entrenamiento sea alta. La formulación general es una **optimización binivel (bilevel optimization)**: el nivel interior entrena el modelo con los datos perturbados y el nivel exterior busca la perturbación δ (con ‖δ‖ₚ ≤ ε) que maximiza el error sobre datos limpios (P1, Eq. 1–2; P4, Eq. 1–2; P7, Eq. 1). P5 añade un matiz importante: el modelo engañado obtiene **alta exactitud de validación** si la validación también está envenenada, por lo que la víctima *cree* que su modelo funciona (P5, §3).

### 1.2 Availability attack / Data Availability Attack (DAA) / data poisoning

- **Data poisoning (envenenamiento de datos):** modificar datos de entrenamiento para que el modelo resultante funcione mal (P5 §2; P11 §2.3.2).
- **Availability attack (ataque a la disponibilidad):** tipo de envenenamiento cuyo objetivo es degradar el rendimiento general (la "disponibilidad" de un modelo útil), no provocar un error puntual. P1 los llama también *generalization attacks*; P10 usa *Data Availability Attack (DAA)*.
- **Clean-label:** el atacante no cambia las etiquetas; solo modifica las imágenes (P11 habla de *clean-label poisoning*; P2 menciona que en CUDA "labels remain the same").
- **Backdoor attack (ataque de puerta trasera):** envenenamiento que inserta un *trigger*; el modelo funciona bien con datos limpios pero falla cuando aparece el trigger. P2 y P6 lo distinguen explícitamente de los unlearnable examples porque el backdoor **no** daña la exactitud limpia.

🔎 **Analogía.** Backdoor = un cerrajero que deja una llave secreta en tu cerradura (todo funciona normal salvo con esa llave). Availability attack = alguien que llena la cerradura de pegamento (no funciona para nadie).

✅ **Explicación técnica.** En backdoor el objetivo es un comportamiento condicional al trigger; en availability attack el objetivo es maximizar el error en la distribución limpia completa.

### 1.3 Shortcut learning (aprendizaje por atajos)

🔎 **Analogía.** P6 da un ejemplo propio: si casi todas las fotos de vacas están sobre pasto, la red puede aprender "verde = vaca" en lugar de la forma de la vaca.

✅ **Explicación técnica.** Las redes neuronales tienden a aprender las características **más simples** que separan las clases en el conjunto de entrenamiento (P6 §2.4; P7 §1). Casi todos los unlearnable examples explotan esto: insertan un patrón sencillo y correlacionado con la etiqueta (un ruido, un píxel, un filtro) que la red aprende *en vez* de la semántica. Cuando ese patrón desaparece en test, la red falla.

### 1.4 Sample-wise vs class-wise

- **Sample-wise:** cada imagen recibe su propia perturbación.
- **Class-wise:** todas las imágenes de una misma clase reciben la *misma* perturbación (o el mismo filtro). P4 lo define así para el "class-wise multiplicative noise" (nota al pie 1).

🔎 **Analogía.** Class-wise = poner la misma pegatina a todos los cuadernos de matemáticas; sample-wise = una pegatina distinta para cada cuaderno.

✅ **Explicación técnica.** Class-wise es más fácil de aprender como atajo (y más barato de generar: P5, Tabla 12, Random(C) ≈3 s frente a Error-min(S) ≈90 min en CIFAR-10), pero también puede ser más fácil de detectar o estimar (P5 §4; P10 §2 menciona que perturbaciones class-wise aditivas pueden recuperarse con la imagen promedio de la clase).

### 1.5 Presupuesto de perturbación (ε, norma Lₚ) y "imperceptible"

La norma **L∞** mide el cambio máximo de cualquier píxel; **L2** mide la magnitud euclidiana global; **L0** cuenta cuántos píxeles cambian. Un presupuesto típico es ‖δ‖∞ ≤ 8/255: ningún píxel cambia más de 8 niveles sobre 255.

🔎 **Analogía.** L∞ = "ningún ladrillo de la pared puede moverse más de 1 cm"; L2 = "la suma total de movimientos de todos los ladrillos no puede pasar de cierto valor"; L0 = "solo puedes mover 5 ladrillos".

✅ **Explicación técnica.** Varios métodos de este bloque **no** usan presupuesto Lₚ: CUDA (P2), EUN (P6), IRP (P10) y KBS (P9) controlan la visibilidad con otros parámetros (blur, pérdida de reconstrucción). P4 lo señala como la razón por la que las defensas clásicas fallan contra CUDA ("absence of norm constraints").

### 1.6 ERM, clean test accuracy y métricas de protección

- **ERM (Empirical Risk Minimization):** entrenamiento estándar, minimizando la pérdida promedio sobre el conjunto de entrenamiento (P2 §3.1; P6 §3.1).
- **Clean test accuracy:** exactitud del modelo sobre el conjunto de prueba limpio. Para métodos de protección, **más baja = mejor protección**; para ataques a la protección o defensas, **más alta = mejor**.

### 1.7 Adversarial Training (AT), PGD, FGSM, TRADES

🔎 **Analogía.** Un boxeador que entrena con un sparring que siempre le pega en su punto débil se vuelve resistente a esos golpes.

✅ **Explicación técnica.** AT resuelve un problema *min–max*: el modelo minimiza la pérdida sobre versiones perturbadas de cada muestra, donde la perturbación (dentro de un radio ρₐ o ε_d) se elige para *maximizar* la pérdida (P2 §3.1). La maximización interna suele resolverse con **PGD (Projected Gradient Descent)**: varios pasos de gradiente proyectados al radio permitido (p. ej., 10 pasos; P7 y P10 usan paso 0.6/255 y radio 4/255). **FGSM** es la versión de un solo paso (P5, Tabla 22). **TRADES** es una variante que suma CE + un término KL entre la predicción limpia y la adversarial (P8). P5 explica por qué AT funciona contra unlearnable examples: destruye las características lineales inyectadas, siempre que el presupuesto de defensa no sea demasiado pequeño respecto al del veneno (P5 §6).

### 1.8 Familias de unlearnable examples que se mencionan como baselines

| Sigla | Nombre | Idea (según cómo la describen los papers) |
|---|---|---|
| **EM / EMin / Err-min / UE** | Error-Minimizing noise | Ruido que **minimiza** la pérdida de entrenamiento: el modelo "cree que no hay nada que aprender" (P6 §2.1; P13 §II-A). |
| **EMax / TAP / AP / Err-max** | Error-Maximizing / Targeted Adversarial Poisoning / Adversarial Poisons | Usar **ejemplos adversariales** como veneno (P6 §2.1; P7 §2.1). |
| **REM / RobustEM / RUE** | Robust Error-Minimizing | Versión min–min–max de EM pensada para resistir AT (P2 §3.2, Eq. 1–2). |
| **NTGA** | Neural Tangent Generalization Attack | Usa *Neural Tangent Kernels* para aproximar la dinámica de entrenamiento (P6 §2.1; P9 §2.1). |
| **DC / DeepConfuse** | — | Generador tipo autoencoder que resuelve la binivel por actualizaciones alternadas (P8 Related Works). |
| **LSP / Syn** | Linearly Separable / Synthetic Perturbations | Parches sintéticos linealmente separables (P10 §2). |
| **AR** | Autoregressive perturbations | Ruido de procesos autorregresivos (P10 §2). |
| **OPS** | One-Pixel Shortcut | Cambiar **un solo píxel** por clase a un color fijo (P6 §2.1; P7 §2.1). |
| **CP** | Contrastive Poisoning | Veneno diseñado contra aprendizaje **auto-supervisado** (P7 B.1; P10 §2). |
| **EntF / INF** | Entangled / Indiscriminate Features | Veneno contra AT (P8 Related Works). |
| **SEP, HYPO, TC, TUE, SEM, PUE, ShortcutGen, Region-n, Random(C)** | — | Otras variantes citadas como baselines (P1, P5, P8, P9); el detalle de cada una no se desarrolla en estos 13 md. |

### 1.9 Defensas que se repiten

| Defensa | Qué hace (según los papers) |
|---|---|
| **Data augmentation**: **Cutout**, **Mixup**, **CutMix**, **AutoAugment**, **RandAugment** | Cutout borra un parche; Mixup mezcla linealmente dos imágenes y sus etiquetas; CutMix pega un recorte de otra imagen y mezcla etiquetas proporcionalmente (P1 §5.4). |
| **ISS (Image Shortcut Squeezing)** | Compresión: **Grayscale** (escala de grises), **JPEG** (p. ej., calidad 10) y **BDR** (Bit-Depth Reduction). Grayscale ataca patrones de baja frecuencia y JPEG de alta frecuencia (P6 §3.2; P10 §2). |
| **UEraser** | Aplica muchas augmentations y elige la que **maximiza** la pérdida (P5, P6, P7). |
| **AVATAR / ECLIPSE / JCDP** | Purificación con **modelos de difusión** (añadir ruido y "des-ruidizar") (P4 Related Work; P3 §III-F). |
| **OP / OPA (Orthogonal Projection)** | Ajustar un modelo lineal (regresión logística) y proyectar ortogonalmente para quitar perturbaciones linealmente separables (P1 §3; P4). |
| **DP-SGD** | Entrenamiento con privacidad diferencial (P4). |
| **Nonlinear transformations** | Grayscale, *pixel manipulation*, erode, dilate (P1). |
| **SSL (Self-Supervised Learning)** | Aprender sin etiquetas (contrastive learning) puede ignorar atajos ligados a la etiqueta (P7, P10). |

### 1.10 Surrogate model, model-free, transferability

- **Surrogate model (modelo sustituto):** red que el protector usa para fabricar la perturbación, suponiendo que el atacante usará algo parecido (P9 §1).
- **Model-free / surrogate-free:** la perturbación se genera **sin entrenar** ninguna red de clasificación (CUDA, OPS, LSP, AR, IRP, KBS — aunque KBS sí entrena autoencoders, no un clasificador sustituto).
- **Transferability:** que la protección siga funcionando cuando la víctima usa otra arquitectura (ResNet, VGG, DenseNet, ViT…).

### 1.11 SSL, contrastive learning, linear probing

🔎 **Analogía.** En lugar de que un profesor diga "esto es un gato", el alumno aprende comparando: "estas dos fotos recortadas son de la misma escena; estas otras son de escenas distintas".

✅ **Explicación técnica.** El **contrastive learning** (p. ej., SimCLR con pérdida **InfoNCE**) entrena un *encoder* para que dos vistas aumentadas de la misma imagen tengan representaciones parecidas y distintas a las de otras imágenes (P7 §2.3). Luego se evalúa con **linear probing**: se congela el encoder y se entrena solo un clasificador lineal encima (P7 B.5; P10 §4.1). Otras variantes: **MoCo**, **BYOL**, **SimSiam** (P7 Tabla 14; P10 Tabla 3).

---

## 2. Resumen completo y exhaustivo de los 13 papers

### 2.1 El problema común

Los 13 papers giran alrededor de una misma tensión: **personas u organizaciones publican datos (fotos, rostros, imágenes con derechos de autor, pares imagen–texto) y terceros los recolectan sin permiso para entrenar modelos.** La respuesta propuesta por la literatura son los **unlearnable examples**: modificar los datos *antes* de publicarlos para que cualquier modelo entrenado con ellos no generalice. A partir de ahí surge una "carrera armamentista":

1. **Proteger mejor** (P2 CUDA, P6 EUN, P8 GUE, P9 KBS, P10 IRP, P11 clustering, P12 PyrFawkes, P13 reversible + watermark).
2. **Romper la protección** (P1 ensembles; P3 CMCR multimodal).
3. **Detectar y neutralizar la protección** (P4 EPD + COIN; P5 detección + SDA+AN + teoría; P7 VESPR).

### 2.2 Resumen paper por paper (versión condensada)

**P1 — Ensembles contra unlearnable data (ataque).** Propone tres marcos de *ensemble learning* (stacking, boosting, bagging) combinados con transformaciones no lineales (grayscale, pixel manipulation, erode, dilate) para entrenar modelos útiles con datos protegidos por 12 métodos (NTGA, EMin, EMax, DC, Syn, AR, REM, OPS, EntF, SEP, HYPO, TC). **[Demuestra]** En CIFAR-10 al menos un ensemble supera 89 % de exactitud limpia frente a todos los métodos (Tablas 1, 2, 4), cuando entrenar una VGG19 sin defensa daba entre 20.61 % (DC) y 85.47 % (SEP) (Tabla 5). **[Demuestra]** Frente a aplicar solo las transformaciones no lineales del trabajo previo, la mejora es pequeña (de −1.13 a +9.38 puntos, Tabla 6) y los propios autores reconocen que "la efectividad … puede cuestionarse" dada la complejidad añadida.

**P2 — CUDA (protección).** Genera datos no aprendibles **convolucionando** cada imagen con un filtro aleatorio **por clase**, generado con una semilla privada; el parámetro *blur* p_b controla cuánto se difumina. Es *model-free* (CIFAR-10 en 10.8 s vs 22.6 h de REM, Tabla 1). **[Demuestra]** Con ResNet-18 en CIFAR-10: 18.48 % con ERM y 44.40 % con AT L∞ 4/255 (limpio: 94.66 % / 89.51 %) (Tabla 2), y resiste grayscale (20.12 %), mixup, cutmix, smoothing, fine-tuning y una defensa adaptativa propia (DAT). **[Demuestra]** Un teorema en un modelo de mezcla gaussiana muestra que el clasificador Bayes óptimo entrenado en datos CUDA pierde exactitud. **[Demuestra]** Si el filtro es universal (igual para todas las clases) la exactitud apenas cae (90.47 %): la clave es que sea *class-wise*.

**P3 — CMCR (ataque multimodal).** Rompe **MEM**, protección de pares imagen–texto para modelos tipo CLIP que añade ruido a la imagen y un *trigger* al inicio del texto. Combina **JCDP** (purificación de imagen con difusión), **ATP** (truncar los primeros 1–5 tokens) y **VCA-BART** (regenerar el texto guiado por la imagen purificada). **[Demuestra]** Recovery Rate promedio de 89.0 % (Tabla I; Fig. 4), mientras JCDP sola recupera 7.6 % y ATP sola 52.3 %. **[Inferencia]** En MS-COCO la recuperación es parcial (Medr 358 vs 6 limpio en Imagen→Texto).

**P4 — EPD + COIN (detección + defensa contra protecciones convolucionales).** **EPD** detecta imágenes convolucionadas mirando la suma de píxeles de los 4 bordes (tienden a ser oscuros) con un SVM lineal. **COIN** "corrompe" la imagen con una interpolación bilineal aleatoria que rompe el patrón multiplicativo por clase. Además propone dos nuevas protecciones convolucionales (**VUDA**, **HUDA**). **[Demuestra]** COIN sube CUDA en CIFAR-10 de 24.92 % a 72.41 % (promedio de 4 arquitecturas; Tabla 2), frente a 47.04 % de AT, y EPD logra ACC 89.21 % / AUC 0.891 en CIFAR-10 y ~99.9 % en ImageNet20 (Tabla 5). **[Demuestra]** En ImageNet100 la ventaja casi desaparece (COIN 37.48 % vs AT 36.60 %; Tabla 3).

**P5 — Detección y defensa + teoría (Zhu, Yu, Gao).** **[Demuestra]** Prueba que ciertos datasets envenenados (Random(C), Region-k) son **linealmente separables** (Teoremas 4.1–4.2), mientras CIFAR-10 limpio solo alcanza 46.53 % con un modelo lineal. Propone dos detectores a nivel de dataset: **Simple Networks Detection** (si un modelo lineal o una red de 2 capas ajusta el dataset >70 %, está envenenado) y **Bias-shifting Noise Test** (sumar ±0.5 a todos los píxeles destruye la semántica pero no el veneno). Propone la defensa **SDA+AN** (augmentations fuertes + ruido adversarial de una red simple) que supera a AT en varios venenos y es ~3–6 veces más barata (Tabla 13). Teoría: si el presupuesto del veneno es ≥4× el de AT, existen venenos que hacen fallar AT (Teorema 6.2). **[Demuestra]** El Bias-shifting test **falla** con CUDA (13.62 %/25.08 %, Tabla 16) — conexión directa con la motivación de P4.

**P6 — EUN (protección).** Combina el filtro por clase de CUDA con el cambio de **un píxel** por clase estilo OPS: x̃ = x * sᵢ + δᵢ. **[Afirma]** Si una defensa neutraliza un atajo, el otro persiste. **[Demuestra]** Con ResNet-18 en CIFAR-10: 14.15 % (ERM), 19.11 % (AT), 22.95 % (ISS Gray+JPEG), 34.17 % (UEraser) (Fig. 6). **[Inferencia]** La ventaja sobre CUDA bajo ISS y UEraser es pequeña (p. ej., 23.17 vs 23.46 bajo JPEG; 34.17 vs 39.47 bajo UEraser; Tablas 7–8).

**P7 — VESPR (defensa).** Muestra que el SSL (contrastive) no es una defensa suficiente en ImageNet-100 y propone combinar SSL + SL con **adversarial training cuyos ejemplos se generan con la pérdida supervisada** (el clasificador envenenado "señala" dónde está el veneno). **[Demuestra]** En ImageNet-100 mejora el peor caso (Psn Min) de 28.92 % (mejor baseline: SL+AT) a 45.20 % y el promedio (Psn Avg) de 46.05 % (SSL) a 55.37 % (Tabla 2).

**P8 — GUE (protección con teoría de juegos).** Formula la protección como un **juego de Stackelberg** (el protector "mueve primero", el entrenador responde) y **demuestra** que existe equilibrio. Entrena un **generador U-Net** con el algoritmo **BOME** y una **pérdida sustituta acotada** L_sur. **[Demuestra]** CIFAR-10: 13.25 % (ERM); el generador entrenado con 60 % de los datos aún deja ~20 % de exactitud (Fig. 3). **[Demuestra]** Frente a AT 4/255, GUE estándar se rompe (76.96 %), y la versión GUE-AT llega a 52.79 % (Tablas 3–4).

**P9 — KBS (protección).** Oculta un **código binario por clase** dentro de la **magnitud y fase de la transformada de Fourier** de cada imagen mediante autoencoders entrenados con pérdidas de reconstrucción y de extracción del código. **[Demuestra]** CIFAR-10: 13.53 % (ERM) y 20.97 % (AT 4/255) frente a 44.40 % de CUDA (Tabla 1), y resiste el ataque RSKFF que rompe CUDA (42.53 % vs 78.53 %, Tabla 5). **[Inferencia]** No gana en todos los escenarios (p. ej., AT L∞ 16/255 en CIFAR-10: 27.46 % KBS vs 19.32 % CUDA; Tabla 4).

**P10 — IRP (protección).** Analiza teóricamente por qué CUDA funciona en redes profundas (gradientes no óptimos + "desplazamiento de picos" entre clases, Teorema 1) y propone **restaurar imperfectamente** la imagen difuminada con una regresión lineal por clase, obteniendo imágenes más nítidas pero aún envenenadas. **[Demuestra]** Mejor protección simultánea en SL y SSL: peor caso CIFAR-10 43.24 % vs 66.58 % de CUDA; ImageNet-100 22.10 % vs 36.34 % (Tablas 1–2), con mejor calidad de imagen (SSIM 0.775 vs 0.561; Tabla 6). Incluye evaluación contra **COIN** (P4): 53.32 % para IRP vs 71.90 % para CUDA (Tabla 8).

**P11 — Envenenamiento de clustering.** Ataca **k-means, GMM y deep clustering (VaDE, Deep k-means)**: (i) evasión en tiempo de decisión proyectando el punto al hiperplano frontera; (ii) envenenamiento moviendo todos los puntos a la frontera; (iii) para deep clustering, añade a cada imagen un **"robust feature" universal elegido al azar** que domina la asignación de cluster. **[Demuestra]** VaDE en MNIST pasa de 92.38 % a 11.25 % de exactitud de clustering (Tabla 2). **[Inferencia]** Los presupuestos (ε=0.3 en MNIST) son grandes comparados con 8/255.

**P12 — PyrFawkes (protección facial).** Aplica "cloaks" (perturbaciones con presupuesto DSSIM) que acercan las *features* de tus fotos a las de otra identidad muy distinta, para que un reconocedor entrenado con ellas te confunda. **[Demuestra]** Protection rate 96.3 % vs Fawkes 83.7 % y LowKey 78.9 % (Tabla II) y menor re-identificación tras JPEG (Tabla I). **[Afirma, sin evidencia en el md]** 100 % contra servicios comerciales (Microsoft, Amazon, Face++), >80 % con imágenes filtradas sin protección, robustez a detección de cloaks: **no hay experimentos de esto en el documento**.

**P13 — Reversible Unlearnable Examples (protección + marca de agua).** Busca **dos** objetivos: impedir el entrenamiento no autorizado y poder **verificar la autoría** si los datos se filtran. Genera una perturbación **por clase** minimizando la **información mutua** entre entrada y salida (implementada como entropía de la predicción), reversible para usuarios autorizados (restan η_c), y una **marca de agua** con **doble extractor** (uno para imágenes con perturbación, otro sin ella). **[Demuestra]** ImageNet-100: exactitud promedio 6.76 % (ε=4) en 5 arquitecturas, 72.23 % tras revertir, y BER ≤0.22 % con JPEG-75 (Tabla II).

### 2.3 Hallazgos transversales (lo que emerge al leerlos juntos)

1. **Ninguna protección es universal.** Cada método de protección muestra al menos un escenario en el que pierde fuerza: CUDA con COIN (P4) o RSKFF (P9); GUE con AT 4/255 (P8); EUN con UEraser (34.17 %, P6); KBS con grayscale (35.24 %, P9); IRP con AVATAR (54.78 %, P10); P13 con JPEG sin módulo diferenciable (63.53 %, P13) o AT ρ=4 (45.84 %).
2. **Ningún ataque/defensa es universal tampoco.** COIN apenas supera a AT en ImageNet100 (37.48 vs 36.60 %, P4); SDA+AN es débil contra Err-max (61.19 % vs 84.75 % de AT, P5); VESPR no alcanza la exactitud limpia en ImageNet-100 (P7); el ensemble de P1 apenas mejora sobre transformaciones no lineales (P1).
3. **La protección parcial casi no existe.** Cuando solo una fracción de los datos está protegida, la exactitud es casi la misma que entrenar solo con la parte limpia (P2 Tabla 5; P9 Tabla 3; P10 Tabla 4). **[Inferencia]** Los datos protegidos dejan de *aportar*, pero no *dañan* al modelo.
4. **La familia convolucional (CUDA → EUN, KBS, IRP, VUDA/HUDA) domina este bloque**, y con ella su contrapartida (EPD/COIN de P4, RSKFF citado por P9).
5. **El "peor caso" es la métrica honesta.** P7 y P10 reportan *Psn Min* o *Max*, que reflejan lo que haría un adversario racional (elegir el veneno o la defensa más favorable).
6. **Reutilización de cifras entre papers.** Varias tablas comparten exactamente los mismos números para los baselines (EM 13.20, TAP 22.51, NTGA 16.27, REM 27.09, CUDA 18.48… aparecen idénticos en P2, P6 y P9). **[Inferencia]** Probablemente se tomaron de la literatura (P2 lo declara: "We adopt the results reported in [11]") en lugar de re-ejecutarse; P9 afirma, sin embargo, que "all methods are tested under identical settings".

---

## 3. Informe detallado por paper

Cada paper sigue la misma plantilla: (1) problema, (2) propuesta, (3) funcionamiento técnico, (4) datos de entrada y salida, (5) evaluación, (6) resultados e interpretación, (7) caso aplicado, (8) fortalezas y limitaciones, (9) cuadro "afirma / demuestra / inferencia / no especificado", (10) relación con los demás papers.

---

### P1 · *Advancing ensemble learning against unlearnable data*

**Autores:** Thushari Hapuarachchi, Kaiqi Xiong (Univ. of South Florida). **Publicación:** Neurocomputing (DOI en el md). **Rol:** ataque contra datos protegidos.

#### P1.1 Problema investigado
Los datos "unlearnable" deberían proteger contra *cualquier* técnica avanzada de aprendizaje. Los autores preguntan: **¿puede el *ensemble learning* romper esa protección?** (P1 §1). Señalan seis desafíos prácticos para aplicar ensembles a datos protegidos: tiempo de entrenamiento, sobreajuste, ajuste de hiperparámetros, dependencia de la calidad de datos, elección/combinación del ensemble y sesgo al dividir los datos (P1 §1).

#### P1.2 Propuesta
Tres marcos que modifican los ensembles clásicos —**stacking, boosting y bagging**— incorporando **transformaciones no lineales** (de un trabajo previo de los mismos autores, citado como [7]).

- **Ensemble learning** — 🔎 *Analogía:* preguntar a varios médicos y combinar sus diagnósticos. ✅ *Técnico:* entrenar varios modelos y combinarlos con un **agregador G** que minimiza la pérdida de validación (P1, Eq. 3).
- **Stacking** — varios modelos *distintos* entrenados en paralelo; un **meta-learner** (aquí regresión logística) aprende a combinar sus probabilidades.
- **Boosting** — modelos en secuencia; cada uno presta más atención a lo que el anterior clasificó mal.
- **Bagging** — modelos entrenados en subconjuntos distintos; se combinan por **votación mayoritaria**.
- **Transformaciones no lineales** — *grayscale* (gris), *pixel manipulation* (reemplazar los tres canales RGB por el valor de un canal: R, G o B → tres variantes), *erode* (expande zonas oscuras) y *dilate* (expande zonas claras), implementadas con OpenCV (P1 §4.4, §5).

#### P1.3 Funcionamiento técnico
- **Formulación de tres niveles (Eq. 4–6):** (i) entrenar θ* de cada modelo fᵢ sobre A(X_T+δ) (datos protegidos transformados por un subconjunto A de transformaciones); (ii) elegir la arquitectura fᵢ* y el subconjunto A* que minimizan la pérdida de validación; (iii) elegir el agregador G. Como hay 2ᴺ subconjuntos posibles de transformaciones, proponen una heurística **"optimal-expanding" O(n²)** (Algoritmo 1): ir agregando transformaciones una a una y quedarse con las que mejoran la exactitud de validación.
- **Stacking (Alg. 2):** aumentar datos → entrenar VGG19, VGG16, ResNet50/101 y "Extended-VGG" (VGG + 4 capas densas de 1024/512/128/64) → obtener probabilidades sobre el train → **regresión logística (solver liblinear)** como meta-learner. Se añaden modelos hasta alcanzar una exactitud objetivo α (90 % u 89 %; 98 % en MNIST).
- **Boosting (Alg. 3):** las imágenes mal clasificadas se **re-aumentan y se repiten** (pesos 1:5, 2:5 o 2:4 entre bien y mal clasificadas), porque "simplemente aumentar el peso de una imagen no ayuda cuando es unlearnable" (P1 §4.3). 10 o 15 iteraciones; voto final. VGG19 preentrenada en ImageNet, ≤30 épocas.
- **Bagging (Alg. 4):** 5 subconjuntos **con solapamiento** (a diferencia del bagging clásico), cada uno aumentado; VGG19; voto mayoritario.

#### P1.4 Datos que necesita y resultado que produce
- **Entrada:** dataset protegido etiquetado (CIFAR-10 de 12 métodos; también MNIST, ImageNet de 2 clases y CIFAR-100 en experimentos adicionales), modelos preentrenados de Keras, conjunto de validación.
- **Salida:** un conjunto de modelos + agregador que clasifica imágenes limpias con alta exactitud.

#### P1.5 Cómo fue evaluado
Clean test accuracy sobre CIFAR-10 limpio, media ± desviación de 5 repeticiones. Baseline: ResNet50 con las mismas transformaciones. Comparación con CutMix, Mixup, AT (PGD 4/255, paso 0.8/255) y transformaciones no lineales solas, todos con VGG19 (Tabla 5). TensorFlow 2.4.1, GPU GTX 1070 Ti.

#### P1.6 Resultados clave e interpretación

**Tabla P1-a (P1, Tablas 1, 2, 4 y 5; CIFAR-10, exactitud limpia %):**

| Dataset protegido | VGG19 sin ataque | Baseline ResNet50 + transf. | Stacking | Boosting | Bagging 75 % | Solo transf. no lineales | Mejor de P1 |
|---|---|---|---|---|---|---|---|
| NTGA | 36.57 | 65.11 | 90.17 | 89.23 | 89.03 | 87.75 | 90.17 |
| EMin | 24.34 | 73.88 | 90.15 | 89.80 | 90.84 | 81.46 | 90.84 |
| EMax | 82.35 | 66.18 | 90.30 | 89.04 | 90.69 | **91.82** | 90.69 |
| DC | 20.61 | 62.84 | 90.17 | 89.21 | 90.04 | 86.74 | 90.30 |
| Syn | 28.68 | 59.52 | 90.11 | 90.05 | 91.51 | 87.37 | 91.51 |
| AR | 49.38 | 71.37 | 90.00 | 89.28 | 91.60 | 86.63 | 91.60 |
| REM | 44.04 | 69.13 | 89.07 | 87.77 | 89.15 | 83.68 | 89.15 |
| OPS | 22.16 | 51.09 | 89.71 | 88.60 | 90.38 | 86.64 | 90.38 |
| EntF | 83.55 | 60.75 | 90.96 | 90.14 | 90.12 | 89.48 | 90.96 |
| SEP | 85.47 | 49.34 | 89.89 | 90.20 | 89.66 | 87.60 | 90.20 |
| HYPO | 35.36 | 67.99 | 91.37 | 87.86 | 89.93 | 88.73 | 91.37 |
| TC | 83.67 | 80.24 | 89.46 | 91.04 | 90.85 | 90.04 | 91.04 |

*(La columna "Mejor de P1" es la que los autores usan en la Tabla 5 —"average of the highest test accuracy obtained by the three ensemble learning frameworks"—. Coincide con el máximo de las columnas Stacking/Boosting/Bagging en 11 de 12 filas; en DC la Tabla 5 dice 90.30 mientras el máximo de las Tablas 1, 2 y 4 es 90.17 (stacking): **[Inferencia]** pequeña inconsistencia interna no explicada.)*

**Interpretación.**
- **[Demuestra]** Las 12 protecciones evaluadas dejan de proteger frente a estos marcos: todas quedan en ~89–92 % cuando el limpio ronda 95 % (cifra que P1 cita de otro trabajo).
- **[Demuestra]** La mayor parte del efecto viene de las **transformaciones no lineales** (columna "Solo transf."): la ganancia adicional del ensemble va de −1.13 (EMax) a +9.38 (EMin) puntos (P1, Tabla 6). Los autores lo reconocen: "the effectiveness of adopting the proposed method can be questioned" (P1 §5.4).
- **[Demuestra]** Los modelos ResNet rinden peor que los VGG con datos protegidos (p. ej., ResNet50 49.34 % vs VGG19 88.04 % en SEP; Fig. 3) y lo controlan en la Tabla 8 (VGG19 vs ResNet50 con todo igual: EMin 81.46 vs 74.52). Los autores lo atribuyen a que varias protecciones se generaron con ResNet18 como sustituto.
- **[Demuestra]** En bagging, más solapamiento mejora (Tabla 3, NTGA: 0 % → 84.35 %; 75 % → 89.17 %; 100 % → 89.4 %).
- **[Demuestra]** En CIFAR-100 (clean VGG16 61.38 %) stacking llega a 61.31/61.60/61.21 % en EMin/Syn/OPS, mientras bagging queda en 56–61 % (Tablas 10–12), por pocos ejemplos por clase.
- **Costo** (Tabla 7, EMin): stacking 2 h 15 min, boosting 3 h 57 min, bagging 2 h 42 min; hasta ~20 GB de memoria (Fig. 5).

#### P1.7 Caso aplicado
*Una startup recolecta 50 000 fotos de productos de una tienda online que las protegió con EMin (exactitud del modelo de la startup: 24.34 % con VGG19).* Si la startup aplica las transformaciones no lineales (grayscale, pixel manipulation) ya sube a 81.46 %; si además arma un bagging de 5 VGG19 con 75 % de solapamiento, llega a 90.84 %. **Lectura práctica:** la protección EMin no detuvo el uso no autorizado; quien protege debería asumir que el adversario usará trucos de preprocesamiento baratos. El caso también muestra que, desde el punto de vista del atacante, **la transformación es lo que más rinde por unidad de esfuerzo**; el ensemble aporta los últimos puntos.

#### P1.8 Fortalezas y limitaciones
- **Fortalezas:** 12 protecciones evaluadas en un mismo marco; tres familias de ensemble; repetición 5 veces con desviación estándar; experimento controlado ResNet vs VGG (Tabla 8); reporte de tiempos y memoria.
- **Limitaciones declaradas:** modelos poco diversos (casi todos VGG) → mejoras marginales; alto costo; solo arquitecturas convolucionales clásicas (no ViT); principalmente CIFAR-10; los tres marcos se ejecutan por separado (P1 §6).
- **Limitaciones inferidas:** **[Inferencia]** el Algoritmo 2 detiene el stacking cuando la *exactitud de test* supera α, y los hiperparámetros se eligieron "based on the final test accuracy" (P1 §4.1, §5.5): usar el conjunto de test para decidir introduce un sesgo optimista. **[Inferencia]** No se evalúan protecciones convolucionales (CUDA, EUN, IRP, KBS) que, según P2/P6/P9/P10, resisten grayscale mejor que las aditivas.

#### P1.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Las 12 protecciones fallan en evitar el uso no autorizado" y "hay una necesidad urgente de nuevas protecciones" (abstract). |
| **Demuestra** | >89 % en CIFAR-10 para al menos un ensemble en los 12 casos (Tablas 1, 2, 4); mejora pequeña sobre transformaciones solas (Tabla 6); ResNet50 < VGG19 en datos generados con ResNet18 (Tabla 8). |
| **Inferencia** | La parte "ensemble" aporta poco frente al preprocesamiento; la selección con test set puede inflar resultados. |
| **No especificado** | Transformaciones exactas por dataset, learning rates y épocas por experimento (remitidos a material suplementario no incluido en el md). |

#### P1.10 Relación con los demás papers
- Comparte con **P5** y **P7** la idea de que **AT** es una defensa fuerte (P1 Tabla 5: AT recupera 83.41 % en NTGA, pero solo 11.17 % en OPS).
- Su observación "grayscale/pixel manipulation neutraliza perturbaciones por canal" es exactamente la que **CUDA (P2)** dice resistir (grayscale 20.12 %) y la que **KBS (P9)** no resiste tan bien (35.24 %).
- A diferencia de **P3** (que purifica los datos), P1 **no limpia** los datos: cambia cómo se entrena.

---

### P2 · *CUDA: Convolution-based Unlearnable Datasets*

**Autores:** Vinu Sankar Sadasivan, Mahdi Soltanolkotabi, Soheil Feizi. **Rol:** protección (model-free). **Código:** github (vinusankars).

#### P2.1 Problema investigado
Los métodos previos (EM, TAP, NTGA) son vulnerables a AT; REM resiste AT pero (a) es **costoso** (22.6 h en CIFAR-10, 51.2 h en ImageNet-100; Tabla 1), (b) depende del modelo y debe re-entrenarse por dataset, y (c) es **sensible al presupuesto** de AT: REM generado con ρ_u=8/255 y ρ_a=4/255 cae a 48.16 % con AT 4/255 pero sube a 78.71 % con AT L∞ 8/255 y a 79.65 % con AT L2 0.75; además, en gris llega a 70.76 % (P2 §3.2).

#### P2.2 Propuesta
**CUDA**: convolucionar cada imagen con un **filtro aleatorio por clase** generado con una **clave privada (semilla)**.

🔎 **Analogía.** Es como si cada clase de fotos se tomara con un lente ligeramente "sucio" distinto: todas las fotos de perros con el lente A, las de gatos con el lente B. La red aprende a reconocer *la mancha del lente* y no al animal.

✅ **Técnico.** Para cada clase i se crea sᵢ ∈ ℝ^{k×k}: un elemento aleatorio vale 1 y el resto se muestrea de 𝒰(0, p_b). La imagen protegida es x̃ = (x * sᵢ)/MAX(x * sᵢ) (reescalado a [0,1]). **p_b** (*blur parameter*) controla el difuminado (P2 §3.3). Por el **teorema de convolución**, convolucionar en el espacio equivale a **multiplicar en el dominio de Fourier**, por lo que CUDA introduce **ruido multiplicativo** (no aditivo), que puede ser grande sobre todo en los bordes sin destruir la semántica (P2 §1).

#### P2.3 Funcionamiento técnico y teoría
- **Teoría (§4):** datos como mezcla gaussiana de dos modos 𝒩(yμ, I). El clasificador Bayes óptimo limpio tiene exactitud φ(‖μ‖₂) (Lemma 1). La convolución 1D con filtro [a,1,a] equivale a multiplicar por una **matriz de Toeplitz tridiagonal** T(d; a, 1, a) (Lemma 3); los datos CUDA siguen siendo una mezcla gaussiana 𝒩(yA_yμ, A_y²) (Lemma 2) cuyo clasificador óptimo es **cuadrático** (Lemma 4). El **Teorema 2** da una cota superior a su exactitud sobre datos limpios y muestra que **existe** a₁ tal que τ_D(P̃) < τ_D(P), es decir, el veneno funciona.
- **Por qué funciona (empírico, §5.2):** con un filtro **universal** (el mismo para todas las clases) ResNet-18 obtiene 90.47 %; con filtros por clase, 18.48 %. Un modelo entrenado en CUDA logra 99.91 % sobre el test *convolucionado con los filtros correctos* y 2.53 % si se permutan los filtros entre clases → **la red aprendió la relación filtro↔etiqueta** (y los autores notan que esto podría servir como backdoor, A.11).

#### P2.4 Datos y resultado
- **Entrada:** dataset etiquetado + clave privada; parámetros k y p_b (k=3, p_b=0.3 en CIFAR; k=9, p_b=0.06 en ImageNet-100).
- **Salida:** dataset difuminado por clase. El publicador debe **borrar las imágenes limpias** (si se filtran pares limpio/CUDA, la protección puede romperse; §5.2 Limitaciones).

#### P2.5 Evaluación
CIFAR-10, CIFAR-100, ImageNet-100 (+ Tiny-ImageNet en apéndice); ResNet-18, VGG-16, WRN-34-10, DenseNet-121 (+ DeIT, EfficientNetV2-S, MobileNetV2). 100 épocas, SGD. Baselines EM, TAP, NTGA, REM **con resultados tomados de Fu et al. [11]** (P2 §5.1).

#### P2.6 Resultados clave e interpretación

**Tabla P2-a (P2, Tablas 2 y 4; ResNet-18, exactitud limpia %; menor = mejor protección):**

| Dataset | Entrenamiento | Limpio | EM | TAP | NTGA | REM | **CUDA** |
|---|---|---|---|---|---|---|---|
| CIFAR-10 | ERM | 94.66 | 13.20 | 22.51 | 16.27 | 27.09 | 18.48 |
| CIFAR-10 | AT L∞ 4/255 | 89.51 | 88.62 | 88.02 | 88.96 | 48.16 | **44.40** |
| CIFAR-100 | ERM | 76.27 | 1.60 | 13.75 | 3.22 | 10.14 | 12.69 |
| CIFAR-100 | AT L∞ 4/255 | 64.50 | 63.43 | 62.39 | 62.44 | **27.10** | 34.34 |
| ImageNet-100 | ERM | 80.66 | 1.26 | 9.10 | 8.42 | 13.74 | 8.96 |
| ImageNet-100 | AT L∞ 4/255 | 66.62 | 63.40 | 63.56 | 63.06 | 41.66 | **38.68** |

**Robustez de CUDA (P2, §5.2 y apéndice):** AT L∞ 8/255 → 32.85 %; 16/255 → 19.32 %; L2 0.25/0.50/0.75 → 39.05/51.19/51.14 %; grayscale 20.12 %; mixup 25.53 %; cutout 25.80 %; cutmix 26.93 %; autoaugment 34.09 %; orthogonal regularization 50.72 %; randomized smoothing 43.85 %; fine-tuning de ResNet-18 preentrenada 42.42 %; random blurring 9.2–13.37 %; **DAT** (*Deconvolution-based Adversarial Training*, defensa adaptativa propuesta por los autores que aprende filtros de deconvolución por clase) 39.05/46.21/38.48 % con filtros 3/5/7.

**Interpretación.**
- **[Demuestra]** CUDA es mucho más barato que todos los baselines (segundos vs horas).
- **[Demuestra]** Bajo AT, CUDA es mejor que REM en CIFAR-10 e ImageNet-100, pero **peor en CIFAR-100** (34.34 vs 27.10) y en WRN-34-10 (53.02 vs 48.39; Tabla 3). La afirmación "CUDA outperforms REM" se limita a esos casos, y los autores lo escriben así ("with CIFAR-10 and ImageNet-100").
- **[Demuestra]** Con protección parcial y ERM (Tabla 5), la exactitud "mixed" ≈ la de entrenar solo con la parte limpia (p. ej., 80 % protegido: 85.61 % vs 84.30 %). **[Inferencia]** CUDA no "daña" al modelo cuando hay datos limpios; simplemente no aporta información.
- **[Demuestra]** DAT rompe CUDA si p_b=0.1 (~78 %), por eso eligen p_b=0.3 (A.8; Fig. 1): **la elección de p_b es un compromiso entre visibilidad y robustez**.

#### P2.7 Caso aplicado
*Una fotógrafa de fauna publica 10 000 fotos en su web y quiere impedir que una empresa entrene un clasificador de especies con ellas.* Con CUDA genera las versiones protegidas en segundos y publica solo esas. Si la empresa entrena normalmente, obtiene un modelo con ~18 % de exactitud (en el análogo CIFAR-10); incluso con AT de 4/255 llega a ~44 %. **Pero:** (a) si la fotógrafa protege solo el 20 % de sus fotos, la empresa entrena casi igual de bien con el 80 % restante; (b) si la empresa usa COIN (P4), la protección se debilita fuertemente (72 % en el análogo CIFAR-10); (c) las fotos se verán difuminadas (con p_b grande, visiblemente).

#### P2.8 Fortalezas y limitaciones
- **Fortalezas:** model-free y rapidísimo; fundamento teórico (en un modelo simplificado); amplia batería de defensas, incluida una adaptativa diseñada por los propios autores; explicación causal (filtro universal vs por clase).
- **Limitaciones declaradas:** vulnerable si se filtran pares limpio/protegido; requiere filtros secretos; protección parcial + AT débil; futuro: texto/tabular (P2 §5.2).
- **Limitaciones inferidas:** **[Inferencia]** la perturbación **no está acotada en norma** y es visible (blur), por lo que la comparación contra baselines con 8/255 no es "a igual imperceptibilidad". **[Inferencia]** la teoría es para dos clases gaussianas con convolución 1D, lejos de redes profundas (crítica que también formula P10 §1).

#### P2.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "CUDA es robusto a varias augmentations y entrenamientos" y "a defensas adaptativas" (abstract). |
| **Demuestra** | Tablas 2–5; Teorema 2; experimentos de filtro universal y filtros permutados. |
| **Inferencia** | Robustez dependiente de p_b; ventaja frente a REM no uniforme; protección parcial sin daño. |
| **No especificado** | Métricas cuantitativas de calidad visual (PSNR/SSIM) en el md; P10 luego reporta SSIM 0.561 para CUDA en ImageNet-100. |

#### P2.10 Relación con los demás papers
CUDA es el **eje** de este bloque: **P4** lo detecta y corrompe (EPD/COIN), **P5** muestra que su test de bias-shift no lo detecta, **P6 (EUN)** lo combina con OPS, **P9 (KBS)** critica sus bordes oscuros y "hash collisions", **P10 (IRP)** lo analiza teóricamente y lo mejora, **P7 (VESPR)** lo usa como el veneno más difícil en ImageNet-100 (SL 6.10 %).

---

### P3 · *Cross-Modal Collaborative Recovery* (CMCR)

**Autores:** Ruijia Li, Zijiao Zhang, Ximin Huang, Hongwei Hu, Xining Gao (Zhengzhou Univ.). **Rol:** ataque contra protección **multimodal**.

#### P3.1 Problema investigado
Los modelos **visión-lenguaje (VLMs)** como **CLIP** se entrenan con pares imagen–texto de internet mediante *contrastive learning* (P3 §I). La protección **MEM** (*Multi-step Error Minimization*, única protección multimodal pública según P3 §II-A) añade ruido a la imagen y un **trigger** (tokens insertados) al texto para crear correlaciones espurias. El paper evalúa si esa protección resiste un ataque **coordinado entre modalidades** (P3 §I).

- **Trigger** — 🔎 *Analogía:* una "palabra clave" pegada al inicio de cada pie de foto que el modelo aprende a usar como atajo. ✅ *Técnico:* en MEM, tᵢ se concatena al texto Tᵢ (D' = {(Iᵢ+δᵢ, Tᵢ ⊕ tᵢ)}, Eq. 1).

#### P3.2 Propuesta
**CMCR**, con tres módulos:
1. **JCDP** (*Joint-Conditional Diffusion Purification*, adoptado de trabajo previo [20]): purifica la imagen con un **modelo de difusión**. 🔎 *Analogía:* echar arena sobre un dibujo hasta tapar las manchas y luego "repintarlo" guiándose por el original. ✅ *Técnico:* proceso *forward* que añade ruido gaussiano durante T_p pasos (Eq. 10–11) y proceso *reverse* guiado por la distancia de píxeles (MSE) y la distancia perceptual **LPIPS** respecto a la imagen original (Eq. 12–14).
2. **ATP** (*Adaptive Text Purification*): elimina los primeros k tokens (k=1…5) y elige el k que mejor alinea texto e imagen purificada según una pérdida contrastiva (Eq. 4–6).
3. **VCA-BART**: si ATP no basta (umbral 0.75), un **BART** (modelo de lenguaje *encoder–decoder* preentrenado que genera texto) con **cross-attention** a las características de la imagen purificada regenera el texto (Eq. 7–9). 🔎 *Analogía:* si arrancar la primera palabra no limpia el pie de foto, se reescribe el pie mirando la foto. ✅ *Técnico:* en cada capa del decoder, Q viene del texto y K,V de la imagen; se minimiza −Σ log P(tᵢ | t₍<ᵢ₎, CNN(I*)).

La pérdida total declarada es L_total = L_c (consistencia cruzada) + λ₁L_f (fidelidad de imagen) + λ₂L_t (fluidez del texto) (Eq. 3).

#### P3.3 Funcionamiento técnico (flujo)
Primero JCDP purifica imágenes → ATP intenta truncar el trigger → si el desempeño no alcanza el umbral, se activa VCA-BART (P3 §III-C, Fig. 1).

#### P3.4 Datos y resultado
- **Entrada:** dataset multimodal protegido por MEM; modelo de difusión preentrenado; BART preentrenado; un codificador de imagen/texto para calcular alineación.
- **Salida:** dataset purificado D* con el que un modelo CLIP-like recupera rendimiento de recuperación (retrieval).

#### P3.5 Evaluación
Flickr8K (8 000 imágenes / 40 000 pares), Flickr30K (31 000 / 155 000), MS-COCO (123 000 / 615 000). Métricas:
- **Recall@K**: proporción de veces que la respuesta correcta está en los K primeros resultados (más alto = mejor).
- **Medr (Median Rank)**: posición mediana de la respuesta correcta (más bajo = mejor).
- **Recovery Rate** = (Medr_protegido − Medr_purificado)/(Medr_protegido − Medr_limpio) × 100 (Eq. 15).
Baselines: JCDP sola y **ONION** (defensa contra backdoors textuales, ref. [25]). GPU RTX 4090.

#### P3.6 Resultados clave e interpretación

**Tabla P3-a (P3, Tabla I; R@10 / Medr):**

| Método | F8k I→T | F8k T→I | F30k I→T | F30k T→I | COCO I→T | COCO T→I |
|---|---|---|---|---|---|---|
| Limpio | 27.4 / 42 | 26.1 / 38 | 48.3 / 13 | 44.8 / 18 | 63.2 / 6 | 57.9 / 7 |
| MEM-3 (protegido) | 4.3 / 312 | 3.6 / 265 | 3.9 / 409 | 2.3 / 316 | 1.8 / 1679 | 1.3 / 1293 |
| JCDP | 4.7 / 291 | 3.5 / 259 | 4.5 / 386 | 3.2 / 297 | 1.9 / 1535 | 1.5 / 1106 |
| ONION | 12.1 / 108 | 14.9 / 92 | 10.2 / 173 | 8.3 / 168 | 1.8 / 1301 | 1.6 / 904 |
| **CMCR** | **27.5 / 43** | **26.6 / 40** | **40.5 / 49** | **37.1 / 55** | **45.4 / 358** | **40.7 / 293** |

**Interpretación.**
- **[Demuestra]** En Flickr8K, CMCR recupera prácticamente el nivel limpio. **[Inferencia]** Calculando la Eq. 15 sobre la Tabla I obtengo ≈99.6, 99.1, 90.9, 87.6, 79.0 y 77.8 % para las seis columnas, cuyo promedio es ≈89.0 %, **consistente** con la cifra reportada.
- **[Inferencia]** La "recuperación" se mide con Medr; en **Recall@10** MS-COCO queda lejos del limpio (45.4 vs 63.2), y el Medr 358 frente a 6 indica que la recuperación en el dataset grande es **parcial**.
- **[Demuestra]** Ablación (Fig. 4): JCDP sola 7.6 %, ATP sola 52.3 %, CMCR 89.0 %. **La parte textual es la que más aporta**: el trigger en posición fija es el punto débil de MEM.
- **[Demuestra]** Contra MEM-5 (Tabla II), VCA-BART supera levemente a ATP en datasets complejos (COCO I→T Medr 340 vs 375).
- **Costo** (§IV-F): en MS-COCO, purificar cuesta ~46 h (38 h JCDP con 200 pasos de difusión + 8 h texto) y entrenar 15 h → 61 h frente a 52 h de adversarial training; se amortiza tras ~4 entrenamientos.

#### P3.7 Caso aplicado
*Un museo publica 30 000 fotos con descripciones protegidas con MEM para que nadie entrene un buscador imagen-texto.* Un tercero aplica CMCR: elimina las primeras palabras de cada descripción (donde MEM puso el trigger), purifica imágenes con difusión y reescribe las descripciones que sigan "raras". En un escenario similar a Flickr30K, su buscador pasaría de un Medr de 409 (inútil) a 49 (útil, aunque peor que 13 del limpio). **Lección para el museo:** proteger cada modalidad por separado y con triggers en posición fija es frágil (P3 §III-B propone triggers dinámicos y restricciones de consistencia cruzada).

#### P3.8 Fortalezas y limitaciones
- **Fortalezas:** primer ataque multimodal coordinado en este bloque; ablación clara; análisis de costo; prueba de transferencia a RN101 y ViT-B/32 (Tabla III).
- **Limitaciones declaradas:** solo evalúa MEM; ATP asume trigger al inicio (VCA-BART mitiga posiciones aleatorias).
- **Limitaciones inferidas / no especificado:** **[No especificado]** qué significan los sufijos de MEM-3 y MEM-5, cómo se optimiza L_total (Eq. 3), valores de λ₁, λ₂, ε de MEM, modelo de difusión usado, **con qué datos se entrena VCA-BART** (si usa texto limpio, el atacante necesitaría datos extra). **[Inferencia]** La Eq. 9 condiciona en "Medr ≥ τ_thresh" mientras el texto define el umbral 0.75 sobre recall/Recovery Rate: inconsistencia de notación. **[Inferencia]** La Tabla III reporta para MS-COCO con RN50 un Medr purificado de 102/115, distinto del 358/293 de la Tabla I sin explicación. No se reportan repeticiones ni varianza.

#### P3.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Las protecciones multimodales actuales tienen vulnerabilidades e insuficiente robustez" (abstract). |
| **Demuestra** | Recovery 89.0 % y ablación (Tabla I, Fig. 4); transferencia (Tabla III). |
| **Inferencia** | Recuperación incompleta en COCO; la vulnerabilidad principal es textual. |
| **No especificado** | Hiperparámetros, entrenamiento de VCA-BART, varianza. |

#### P3.10 Relación con los demás papers
- Igual que **P1**, es un ataque a la protección, pero **P3 purifica los datos** (preprocesamiento) mientras P1 cambia el entrenamiento.
- La purificación por difusión es la misma familia que **AVATAR/ECLIPSE** usadas como baselines en **P4** (débiles contra CUDA) y que **P10** encuentra útil contra CUDA e IRP (AVATAR: 55.93 % y 54.78 %).
- Es el único paper del bloque centrado en **multimodalidad**; **P13** también usa una "marca" en los datos, pero para verificar autoría.

---

### P4 · *Detecting and Corrupting Convolution-based Unlearnable Examples* (EPD + COIN)

**Autores:** Minghui Li, Xianlong Wang et al. (HUST, Griffith). **Publicación:** AAAI 2025. **Rol:** detección + defensa contra protecciones convolucionales; además dos protecciones nuevas.

#### P4.1 Problema investigado
Las protecciones convolucionales (CUDA) usan ruido **multiplicativo sin restricción de norma** y "han derrotado todas las defensas" (Tabla 1 de P4: Cutout, Mixup, CutMix, DP-SGD, AT, AA, AVATAR, OP, ISS-G, ISS-J, ECLIPSE). Además, al ser visualmente discretas, primero hay que **detectarlas** (P4 Introducción).

#### P4.2 Propuesta
1. **EPD (Edge Pixel-based Detector):** detector de imágenes convolucionadas basado en los **bordes**.
2. **COIN (COrrupting via random matrix multiplication by employing bilinear INterpolation):** transformación aleatoria multiplicativa que rompe el patrón por clase.
3. **VUDA y HUDA:** dos nuevas protecciones convolucionales con filtros **verticales** y **horizontales** por clase, para probar que COIN generaliza (Eq. 3–4).

#### P4.3 Funcionamiento técnico
- **Modelo simplificado (GMM):** la imagen protegida es x_u = A_y · x (Eq. 5), donde A_y es la matriz multiplicativa de la clase y (tridiagonal en CUDA).
- **Dos métricas nuevas:**
  - **Θ_imi** (*intra-class matrix inconsistency*): varianza media de las matrices multiplicativas *dentro* de una clase.
  - **Θ_imc** (*inter-class matrix consistency*): similitud coseno entre las matrices promedio de *distintas* clases.
  - **Hipótesis (validada en Fig. 2):** aumentar cualquiera de las dos mejora la exactitud del modelo entrenado.

🔎 **Analogía.** Si cada clase tiene "su" lente sucio, COIN le pone a *cada foto* encima otro lente sucio aleatorio distinto. Ahora las fotos de una misma clase ya no comparten la misma mancha (más inconsistencia intra-clase) y las manchas de clases distintas se parecen más entre sí (más consistencia inter-clase): la mancha deja de ser un atajo útil.

✅ **Técnico.** En el modelo matricial se multiplica por una matriz aleatoria A_r: se muestrea s ~ 𝒰(−α, α); mᵢ = ⌊sᵢ⌋ es un desplazamiento entero y nᵢ = sᵢ − mᵢ un peso; la fila i tiene (1−nᵢ, nᵢ) desplazados mᵢ posiciones (Eq. 6). En imágenes reales esto equivale a una **interpolación bilineal aleatoria**: para cada píxel se toman desplazamientos aleatorios (s_x, s_y) en [−α, α], se buscan los 4 vecinos (con *wrap-around* módulo W, H) y se interpola (Eq. 7–15). α = 2.0.

- **EPD:** suma los valores de píxel de los 4 bordes (arriba, abajo, izquierda, derecha) por canal → vector de 12 valores (Eq. 16–17) → **SVM lineal** (C_p = 0.1, umbral 0). Intuición: "los bordes de las UE convolucionales tienden al negro" (P4 Introducción, Fig. 1a). **[Inferencia]** esto es coherente con lo que P9 llama *border effects* de la convolución.

#### P4.4 Datos y resultado
- **EPD — entrada:** imágenes sospechosas + un SVM preentrenado con ejemplos convolucionales y no convolucionales. **Salida:** etiqueta 1/0 por imagen.
- **COIN — entrada:** imágenes detectadas como convolucionales. **Salida:** imágenes transformadas para entrenar.

#### P4.5 Evaluación
CIFAR-10/100 (32×32), ImageNet20 (64×64), ImageNet100 (224×224); ResNet18/50, VGG16, DenseNet121, MobileNetV2; SGD 80 épocas. EPD sobre 50 000 imágenes mezclando 6 tipos de UE (3 convolucionales + 3 acotadas) en 7 configuraciones S1–S7. Métricas: exactitud limpia; ACC y **AUC** (área bajo la curva ROC: 1 = detector perfecto, 0.5 = azar).

#### P4.6 Resultados clave e interpretación

**Tabla P4-a (P4, Tablas 2 y 3; exactitud limpia promedio %; más alto = mejor defensa):**

| Defensa | CUDA CIFAR-10 | CUDA CIFAR-100 | CUDA ImageNet20 | CUDA ImageNet100 |
|---|---|---|---|---|
| Sin defensa | 24.92 | 13.42 | 37.5 | 22.60 |
| Mixup / CutMix / Cutout | 26.02 / 24.98 / 23.24 | 15.99 / 13.39 / 13.28 | 36.8 / 34.6 / 43.4 | 24.43 / 18.04 / 24.04 |
| DP-SGD | 24.89 | 13.02 | — | — |
| AVATAR | 30.48 | 13.03 | — | — |
| AA | 39.63 | 18.55 | — | — |
| OP | 30.70 | 18.33 | — | — |
| ISS-G / ISS-J | 23.44 / 41.65 | 8.85 / 30.18 | 13.5 / 45.1 | 11.00 / 30.18 |
| AT | 47.04 | 33.85 | 52.5 | 36.60 |
| ECLIPSE | 32.16 | 18.40 | — | — |
| **COIN** | **72.41** | **47.41** | **60.9** | **37.48** |

- VUDA/HUDA (Tabla 4, CIFAR-10 promedio): sin defensa 41.30 / 40.53; mejor baseline 45.15 (ISS-J) / 49.21 (OP); **COIN 72.44 / 73.05**.
- COIN también ayuda contra dos protecciones acotadas: URP 16.8 → 81.1 % y OPS 28.4 → 80.1 % (§Evaluación).
- **EPD (Tabla 5):** CIFAR-10 ACC 87.63–90.94 % (AUC 0.876–0.909); ImageNet20 ACC 99.89–99.93 % (AUC ≈0.998–0.999).

**Interpretación.**
- **[Demuestra]** En imágenes pequeñas, COIN supera a todas las defensas por 19–45 puntos.
- **[Demuestra]** En ImageNet100 (224×224) la ventaja casi desaparece: COIN 37.48 % vs AT 36.60 %; con ResNet50 AT es mejor (36.80 vs 35.38) (Tabla 3). **[Inferencia]** La defensa sigue lejos de la exactitud limpia en alta resolución (CUDA reporta 80.66 % limpio en ImageNet-100 con otro setup).
- **[Demuestra]** Existe un óptimo de α: demasiado pequeño no rompe el patrón, demasiado grande destruye la imagen (Fig. 6a).

#### P4.7 Caso aplicado
*Una plataforma que entrena modelos con imágenes descargadas sospecha que algunos proveedores las protegieron con CUDA.* Pasa todas las imágenes por EPD (un SVM sobre 12 números por imagen, muy barato). Las marcadas como convolucionales se transforman con COIN antes de entrenar; las demás se dejan intactas. En un escenario tipo CIFAR-10, el modelo pasaría de ~25 % a ~72 % de exactitud. **Advertencia:** si las imágenes fueran de alta resolución (tipo ImageNet100), la mejora sería modesta (~37 %), y EPD podría ser evadido por una protección que no deje bordes oscuros (**[Inferencia]**, no probado en el paper).

#### P4.8 Fortalezas y limitaciones
- **Fortalezas:** primera defensa específica contra convolucionales (según los autores); métricas Θ_imi/Θ_imc que dan una intuición formal; nuevas protecciones para no sobreajustar la defensa a CUDA; detector muy simple y barato.
- **Limitaciones declaradas:** "COIN cannot be effective for all types of UE".
- **Limitaciones inferidas:** **[No especificado]** exactitud limpia de referencia en sus tablas; varianza/semillas; si COIN se aplica también en test. **[Inferencia]** EPD no se evalúa contra un protector adaptativo (p. ej., que use relleno no-cero) ni sobre imágenes **limpias** (sus conjuntos S1–S7 mezclan solo UEs). **[Inferencia]** La Tabla 4 rotula una columna "CUDA CIFAR-10" aunque el texto/caption dicen VUDA y HUDA.

#### P4.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Primera detección y defensa contra UEs convolucionales"; "ninguna defensa existente funciona contra ellas". |
| **Demuestra** | Tablas 2–5; hipótesis Θ validada en GMM (Fig. 2, Fig. 4). |
| **Inferencia** | Ventaja limitada en alta resolución; posibles evasiones de EPD. |
| **No especificado** | Clean accuracy de referencia, varianza, comportamiento con imágenes limpias en EPD. |

#### P4.10 Relación con los demás papers
- Es la **respuesta directa a P2 (CUDA)** y es usada por **P10 (IRP)** como defensa de evaluación (CUDA bajo COIN 71.90 %, IRP 53.32 %; P10 Tabla 8).
- **P5** detecta UEs a nivel *dataset* (¿está envenenado?), EPD a nivel *imagen* (¿es convolucional?). P4 explica que por eso no los compara.
- **P6 (EUN)** y **P9 (KBS)** no se evalúan contra COIN/EPD. **[Inferencia]** EUN, al usar la misma convolución que CUDA, probablemente comparte la firma de bordes oscuros; KBS dice evitar los *border effects* (P9 Fig. 3), lo que lo haría menos visible para EPD; ninguna de estas dos hipótesis está probada en los papers.

---

### P5 · *Detection and Defense of Unlearnable Examples*

**Autores:** Yifan Zhu, Lijia Yu, Xiao-Shan Gao (AMSS/CAS). **Publicación:** AAAI 2024. **Rol:** detección (a nivel dataset) + defensa + teoría sobre presupuestos de adversarial training.

#### P5.1 Problema investigado
Los unlearnable examples engañan a la víctima porque su exactitud de validación (envenenada) es alta. Los autores sostienen que **son fáciles de detectar** y que esa detectabilidad revela por qué funcionan: los datos envenenados son **ajustables por modelos muy simples** (P5 §1, §5).

#### P5.2 Propuesta
1. **Teoría de separabilidad lineal** de ciertos datasets envenenados.
2. Dos detectores: **Simple Networks Detection** (Alg. 1) y **Bias-shifting Noise Test** (Alg. 2).
3. Una defensa: **SDA+AN** (*Stronger Data Augmentations with Adversarial Noises*, Alg. 3).
4. Criterios teóricos que relacionan el **presupuesto del veneno** con el **presupuesto de AT**.

#### P5.3 Funcionamiento técnico
- **Separabilidad lineal.** 🔎 *Analogía:* si a cada foto de perro le pegas una pegatina roja y a cada gato una azul, una regla tonta ("¿roja o azul?") clasifica perfecto. ✅ *Técnico:* Teorema 4.1: con veneno por clase de entradas ±ε aleatorias (Random(C)), el dataset envenenado es linealmente separable con probabilidad ≥ 1 − NC(2e^{−dε²/18} + e^{−d/32}); Teorema 4.2 lo extiende a Region-k. Empíricamente, con ε = 8/255 en CIFAR-10, un modelo lineal clasifica 49 963 de 50 000 imágenes envenenadas (§4.1).
- **Linear Separability Rate** (Def. 4.4): mejor exactitud alcanzable por un modelo lineal. En datos limpios: CIFAR-10 46.53 %, CIFAR-100 31.71 %, TinyImageNet 49.38 % (Remark 4.5).
- **Alg. 1 Simple Networks Detection:** entrenar un modelo lineal o una red de 2 capas (ancho = dimensión de entrada); si su exactitud de entrenamiento > B (= 0.7), el dataset está envenenado. Con augmentations comunes la separabilidad lineal se rompe, por eso se usa la red de 2 capas; una de 3 capas ya ajustaría también datos limpios.
- **Alg. 2 Bias-shifting Noise Test:** sumar a *todas* las imágenes un desplazamiento constante ε_b = ±0.5 (todos los píxeles +0.5 o −0.5), entrenar ResNet18 y medir la validación. 🔎 *Analogía:* subir o bajar muchísimo el brillo de todas las fotos: un humano (y una red que aprendió semántica) se confunde, pero la "pegatina" del veneno sigue ahí. ✅ *Técnico:* el veneno domina las características y es inmune a ese desplazamiento (Tabla 1: validación 90.82–100 % en datos envenenados desplazados).
- **Alg. 3 SDA+AN:** (i) entrenar **adversarialmente** una red simple de 2 capas; (ii) generar ruido adversarial con PGD contra esa red y sumarlo a los datos; (iii) entrenar el modelo final (ResNet18) con **augmentations fuertes** de contrastive learning (*random resized crop, horizontal flip, color jitter, random grayscale*). La lógica: si destruyes la detectabilidad (que una red simple ajuste los datos), destruyes el atajo.
- **Teoría de presupuestos.** El *adversarial training set* 𝔅ₚ(S, ε) es el conjunto de todas las versiones perturbadas que AT puede usar (Def. 6.1). **Teorema 6.2:** si se cumple una condición sobre d, N, C y ε, existe un veneno por clase con presupuesto ≤ 4ε que hace linealmente separable 𝔅∞(S^po, ε) → AT con radio ε falla. **Teorema 6.3:** si existe un veneno separable con presupuesto ε, para cualquier η existe uno con presupuesto η+ε robusto a AT de radio η (con ε = Ω(√(log NC / d))). **Teorema 6.4:** con modelo lineal y hinge loss, hace falta un presupuesto mínimo para lograr separabilidad.

#### P5.4 Datos y resultado
- **Detectores — entrada:** un dataset completo sospechoso (no imágenes sueltas). **Salida:** I(D) ∈ {0,1}.
- **Defensa — entrada:** dataset envenenado etiquetado. **Salida:** un modelo entrenado con mejor exactitud limpia.

#### P5.5 Evaluación
CIFAR-10, CIFAR-100, TinyImageNet; venenos Random(C), Region-16, Err-min(S/C), Err-max, NTGA, AR, RobustEM, EntF, TUE (más CP, CUDA, OPS, Hypocritical, Adv Inducing en apéndice). GPU RTX 3090.

#### P5.6 Resultados clave e interpretación

**Tabla P5-a — Detección (P5, Tabla 3 y Tabla 16; exactitud % que alcanza el detector; si > 70 % se declara "envenenado"):**

| Veneno (CIFAR-10) | Lineal | 2-NN | Bias −0.5 | Bias +0.5 |
|---|---|---|---|---|
| Limpio | 46.53 | 57.33 | 49.08 | 42.12 |
| Random(C) | 100.0 | 99.13 | 100.0 | 99.84 |
| Region-16 | 99.87 | 99.98 | 99.98 | 99.64 |
| Err-min(S) | 99.99 | 99.77 | 97.85 | 100.0 |
| Err-max | 82.48 | 97.52 | 99.96 | 99.20 |
| RobustEM | 78.49 | 99.41 | 99.10 | 99.40 |
| AR | 46.96 ✗ | 96.75 | 90.96 | 99.98 |
| CP | 47.49 ✗ | 56.87 ✗ | 81.31 | 78.28 |
| **CUDA** | 97.89 | 80.40 | **13.62 ✗** | **25.08 ✗** |
| OPS | 99.94 | 99.78 | 98.66 | 85.13 |

(✗ = el detector **no** supera el umbral 0.7 y por tanto falla.)

- **Proporción envenenada** (Tabla 4, Bias −0.5): con 60 % envenenado se detecta (73–79 %); con 40 % o menos, la mayoría de venenos queda bajo 0.7.
- **FP/FN** (Tabla 7, B = 0.7, sobre el conjunto de datasets evaluados): Bias+0.5 0 FP/2 FN; Bias−0.5 0/1; Lineal 0/6; 2-NN 0/1.

**Tabla P5-b — Defensa (P5, Tabla 5; CIFAR-10, exactitud limpia %):**

| Veneno | Sin defensa | AT | UEraser | ISS | SDA | **AN+SDA** |
|---|---|---|---|---|---|---|
| Region-16 | 19.86 | 85.78 | 82.66 | 67.11 | 77.20 | **93.51** |
| Err-min(S) | 10.09 | 84.00 | 86.37 | 85.24 | 75.62 | **88.01** |
| Err-max | 7.19 | **84.75** | 47.46 | 84.36 | 56.96 | 61.19 |
| RobustEM | 25.30 | 81.06 | 78.39 | **83.84** | 79.21 | 79.28 |
| NTGA | 11.23 | 84.19 | 82.15 | 85.64 | 78.48 | **89.00** |
| AR | 17.18 | 85.25 | **87.21** | 85.11 | 75.81 | 80.20 |
| EntF | 83.10 | 73.61 | 87.40 | 77.02 | **90.84** | 88.17 |
| TUE | 10.00 | 83.94 | 75.96 | 84.32 | 73.80 | **92.76** |

**Tabla P5-c — AT con distintos presupuestos (P5, Tabla 6; veneno 8/255, sin augmentations):**

| Veneno | 0 | 1/255 | 2/255 | 3/255 | 4/255 | 6/255 | 8/255 | 12/255 | 16/255 |
|---|---|---|---|---|---|---|---|---|---|
| Region-16 | 19.86 | 24.32 | 29.57 | 50.09 | 72.13 | **77.03** | 72.65 | 67.65 | 62.78 |
| Err-min(S) | 10.09 | 10.01 | 10.13 | 18.64 | 69.92 | **76.53** | 72.13 | 67.23 | 61.70 |
| RobustEM | 25.30 | 24.92 | 28.50 | 33.74 | 46.01 | **76.69** | 72.19 | 63.16 | 53.34 |

**Interpretación.**
- **[Demuestra]** La mayoría de venenos acotados son detectables; ningún detector individual detecta todo: el lineal falla con AR y CP; la 2-NN con CP; el bias-shift con CUDA. **[Inferencia]** Combinar detectores reduce el riesgo, y CUDA (no aditivo) escapa justamente al test que explota la aditividad.
- **[Demuestra]** AN+SDA es la mejor defensa en 4 de 8 venenos (Region-16, Err-min(S), NTGA, TUE) y la SDA sola —también propuesta por los autores— lo es en un quinto (EntF); además es ~3.4× (CIFAR-10: 80 vs 270 min) y ~6× (CIFAR-100: 80 vs 480 min) más rápida que AT (Tabla 13), pero **falla con Err-max** (61.19 % vs 84.75 % de AT).
- **[Demuestra]** La Tabla 6 confirma la teoría: con defensa ≤ 2/255 frente a veneno 8/255 (≥4×), AT no sirve (<30 %, por debajo de la tasa lineal 46.53 %); el máximo se alcanza en 6/255; con radios mayores aparece el clásico *trade-off* exactitud–robustez.
- **[Demuestra]** Tabla 8: con presupuesto 2/255, Region-16 y Err-min(S) ya son casi separables (89.06 % y 93.15 %); RobustEM nunca lo es (78.49 % a 8/255) → la separabilidad es **suficiente pero no necesaria** para que un veneno funcione.
- **[Demuestra]** (Apéndice D.1, Tabla 9) con learning rates pequeños y *early stopping*, la exactitud "pico" de los modelos envenenados es mucho mayor que la final (ResNet18 + Random(C): 11.02 % final vs 79.30 % máximo).

#### P5.7 Caso aplicado
*Un equipo de ML compra un dataset de 50 000 imágenes a un proveedor externo.* Antes de entrenar: (1) entrena un clasificador lineal; si obtiene 99 % de exactitud de entrenamiento (cuando lo normal en ese tipo de datos ronda 46 %), sospecha; (2) corre el bias-shift test; (3) si se confirma veneno, en lugar de un AT caro (270 min), aplica AN+SDA (80 min) y recupera ~88–93 % para venenos tipo Region/Err-min/NTGA/TUE. **Pero** si el proveedor usó CUDA, el bias-shift test no lo detecta, y si usó Err-max, AN+SDA no basta (mejor AT).

#### P5.8 Fortalezas y limitaciones
- **Fortalezas:** teoría + experimentos alineados; detectores baratos; defensa más barata que AT; tabla de falsos positivos/negativos; análisis de proporción envenenada.
- **Limitaciones declaradas:** la cota del Teorema 6.2 puede ajustarse más; faltan defensas certificadas; Err-max sigue siendo difícil (§8).
- **Limitaciones inferidas:** **[Inferencia]** la detección es a **nivel de dataset** y requiere ≥60 % envenenado; el umbral B = 0.7 es fijo para datasets con tasas lineales limpias muy distintas (TinyImageNet 2-NN limpio 7.24 %). **[Inferencia]** Las teorías son para venenos aleatorios por clase; no cubren venenos multiplicativos como CUDA.

#### P5.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Unlearnable examples are easily detectable"; "breaking detectability is a key to defending". |
| **Demuestra** | Teoremas 4.1, 4.2, 6.2–6.4 (pruebas en apéndice A); Tablas 3–8, 13, 16. |
| **Inferencia** | Ningún detector es universal; CUDA escapa al bias-shift. |
| **No especificado** | Cómo elegir B para nuevos dominios; detección por imagen. |

#### P5.10 Relación con los demás papers
- **P4** usa este paper como contraste (P5 detecta datasets envenenados en general, EPD detecta imágenes convolucionales) y su Tabla 16 justifica la necesidad de P4.
- La explicación "el veneno es ajustable por modelos simples" se conecta con **P1** (la Orthogonal Projection explota separabilidad lineal) y con **P13** (perturbaciones *class-wise* aditivas, el caso que P5 prueba separable).
- La teoría de presupuestos explica resultados de **P2, P6, P8, P9** en los que AT con radio pequeño no recupera, pero con radio mayor sí (p. ej., GUE: 22.55 % con 2/255 vs 76.96 % con 4/255, P8 Tabla 3).
- Mismo grupo (AMSS/CAS, X.-S. Gao) que **P8 (GUE)**.

---

### P6 · *EUN: Enhanced unlearnable examples generation approach*

**Autores:** Xiaotian Chen, Yang Xu et al. (Guizhou Normal Univ., Wuhan Univ.). **Publicación:** revista Elsevier. **Rol:** protección.

#### P6.1 Problema investigado
Los métodos de protección existentes no resisten varias defensas a la vez: EM/TAP/NTGA fallan contra AT; REM, OPS y CUDA se validaron sobre todo contra AT, pero no contra **ISS** (compresión) ni **UEraser** (P6 §2.1).

#### P6.2 Propuesta
**EUN**: combinar dos atajos de naturaleza distinta.

🔎 **Analogía.** Proteger una casa con una alarma *y* una cerradura: si el ladrón desactiva una, la otra sigue funcionando.

✅ **Técnico.** x̃ = x * sᵢ + δᵢ (Eq. 1): **convolución por clase** (igual que CUDA: un elemento = 1, el resto 𝒰(0, p_b)) **más** un **cambio de un píxel por clase** (estilo OPS) δ.

#### P6.3 Funcionamiento técnico
- **Elección del píxel** (Eq. 2–4): para cada clase se busca la posición σᵢ (máscara) y el color ξᵢ (valores extremos por canal) que maximizan G_i = E[Σⱼ | ‖xⱼ·σᵢ‖_F − ξᵢⱼ |] / Var[ … ], es decir, el píxel que produce el **mayor cambio promedio con la menor varianza** dentro de la clase (búsqueda discreta).
- **Variantes:** **EUN-B** = primero píxel, luego convolución → distorsión visible; **EUN-C** = primero convolución, luego calcular el píxel → el píxel cae en los bordes y el *random cropping* lo elimina. Se usa EUN estándar (P6 §3.3).
- **Argumento de robustez (§3.2, intuitivo, sin prueba):** grayscale y JPEG atacan baja y alta frecuencia respectivamente; un píxel es alta frecuencia extrema (JPEG lo afecta), pero el filtro "no está determinado por la frecuencia", por lo que ISS no lo elimina; UEraser elimina el píxel pero no el filtro; AT tiene menos efecto cuando ambos coexisten.

#### P6.4 Datos y resultado
- **Entrada:** dataset etiquetado; p_b, k (los de CUDA: k=3, p_b=0.3 en CIFAR; k=9, p_b=0.06 en ImageNet-100/CelebA-100).
- **Salida:** dataset protegido.

#### P6.5 Evaluación
CIFAR-10, CIFAR-100, ImageNet-100, **CelebA-100** (100 identidades de CelebA, 2 500 train / 538 test); ResNet-18/50, VGG-16, WRN-34-10, DenseNet-121, **CVT-7-4** y **CCT-7-3×1** (*compact vision/convolutional transformers*). Defensas: AT (10-step PGD), ISS (Gray, JPEG calidad 10, BDR profundidad 2, Gray+JPEG), UEraser (lite, estándar, Max).

#### P6.6 Resultados clave e interpretación

**Tabla P6-a (P6, Tablas 5–8; ResNet-18, CIFAR-10, exactitud limpia %; menor = mejor protección):**

| Escenario | Limpio | EM | TAP | NTGA | REM | OPS | CUDA | **EUN** |
|---|---|---|---|---|---|---|---|---|
| ERM | 94.66 | 13.20 | 22.51 | 16.27 | 27.09 | 26.16 | 18.48 | 14.15 |
| AT L∞ 4/255 | 89.51 | 88.62 | 88.02 | 88.96 | 48.16 | 21.06 | 44.40 | **19.11** |
| ISS BDR | 88.65 | 36.16 | 45.99 | 62.50 | 40.77 | 53.76 | 22.29 | **9.89** |
| ISS Gray | 92.41 | 93.01 | **9.11** | 74.32 | 92.84 | 42.44 | 21.13 | 10.90 |
| ISS JPEG | 85.38 | 81.50 | 83.87 | 69.49 | 82.28 | 82.53 | 23.46 | **23.17** |
| ISS Gray+JPEG | 83.79 | 83.06 | 81.94 | 69.86 | 83.00 | 79.10 | 23.19 | **22.95** |
| UEraser | 93.90 | 93.38 | 84.17 | 84.41 | 91.02 | 73.22 | 39.47 | **34.17** |
| UEraser-Max | 93.98 | 95.24 | 82.47 | 87.94 | 92.54 | 81.84 | 40.85 | **35.07** |

**Otros resultados:** CIFAR-100 AT 4/255: EUN 7.42 % (CUDA 34.34, OPS 11.19); ImageNet-100 AT 4/255: EUN 12.10 % (CUDA 38.68); CelebA-100 con 5 arquitecturas: 2.04–9.67 % (limpio 41.26–69.33 %) (Tablas 4–6). Con AT L∞ 8/255 y 16/255 en CIFAR-10, **OPS** es ligeramente mejor que EUN (14.50 vs 15.51; 11.87 vs 12.44; Tabla 6).

**Interpretación.**
- **[Demuestra]** EUN es la protección más robusta *dentro de esta tabla* frente a AT, ISS y UEraser simultáneamente; su peor caso en CIFAR-10 es UEraser (34.17–35.07 %).
- **[Inferencia]** La mejora sobre CUDA es grande bajo AT (19.11 vs 44.40) y bajo BDR, pero **mínima** bajo JPEG/Gray+JPEG (≈0.2–0.3 puntos) y moderada bajo UEraser (≈5 puntos). Es decir, contra ISS y UEraser casi todo el mérito es del filtro heredado de CUDA.
- **[Inferencia]** Los valores de EM, TAP, NTGA, REM y CUDA en la fila ERM/AT son idénticos a los de P2, lo que sugiere que fueron reutilizados de la literatura (no se declara).
- **[Demuestra]** Más píxeles no mejoran de forma monótona (1–5 píxeles: 14.15, 11.25, 14.45, 14.07, 12.10 %; Tabla 10) → eligen 1 por sigilo.

#### P6.7 Caso aplicado
*Una red social quiere ofrecer a sus usuarios un botón "proteger mis selfies" para que no se usen en un reconocedor de 100 personas (escenario análogo a CelebA-100).* EUN reduce la exactitud del reconocedor a ~2–10 % con varias arquitecturas, y bajo UEraser (la defensa más fuerte probada) se mantiene ~34 % en el análogo CIFAR-10. **Pero:** las imágenes quedan algo difuminadas (como CUDA), no hay métrica de calidad visual reportada, y no se probó contra COIN (P4), que en P10 sube CUDA a ~72 %.

#### P6.8 Fortalezas y limitaciones
- **Fortalezas:** evalúa tres familias de defensa (AT, ISS, UEraser) con múltiples variantes; incluye transformers compactos y un dataset de rostros; ablaciones de p_b y número de píxeles.
- **Limitaciones inferidas:** sin métricas de calidad (PSNR/SSIM); sin varianza; sin evaluación contra purificación por difusión, COIN, detección (P5/P4) ni protección parcial; la justificación de robustez es intuitiva; posible reutilización de cifras de baselines.

#### P6.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Robusto a AT, ISS y UEraser"; "20 % más bajo que CUDA y OPS bajo varios parámetros" (abstract). |
| **Demuestra** | Tablas 2–10; Fig. 6. La diferencia ≥20 puntos se cumple en algunos escenarios (p. ej., AT 4/255 vs CUDA; L2 0.5–0.75 vs CUDA y OPS), no en todos. |
| **Inferencia** | Gran parte de la robustez a ISS/UEraser proviene del filtro de CUDA. |
| **No especificado** | Calidad visual, varianza, costo de la búsqueda del píxel. |

#### P6.10 Relación con los demás papers
- **Hijo directo de P2 (CUDA)** y de OPS. Comparte con **P9 (KBS)** y **P10 (IRP)** la meta de superar a CUDA, pero usa una suma de atajos en vez de cambiar el dominio (KBS: Fourier) o la nitidez (IRP: restauración).
- **P5** y **P1** predicen que el componente OPS (un píxel) es frágil ante transformaciones (P1: OPS → 86.64 % con transformaciones no lineales; P6 lo confirma: UEraser lo elimina).

---

### P7 · *VESPR: Exploiting Supervised Poison Vulnerability to Strengthen Self-Supervised Defense*

**Autores:** Jeremy Styborski, Mingzhi Lyu, Yi Huang, Adams Kong (NTU Singapur). **Rol:** defensa (entrenar un modelo útil a pesar del veneno).

#### P7.1 Problema investigado
Se creía que el **aprendizaje auto-supervisado (SSL)** era una defensa fuerte porque no usa etiquetas, así que no aprende atajos ligados a ellas. Los autores muestran que en **ImageNet-100** el SSL **también aprende el veneno** y queda muy por debajo de entrenar con datos limpios (P7 §1). Además, las defensas existentes casi solo se habían evaluado en CIFAR-10.

#### P7.2 Propuesta
**VESPR** (*Vulnerability Exploitation of Supervised Poisoning for Robust SSL*): un entrenamiento conjunto SSL + SL donde las **perturbaciones adversariales se calculan con la pérdida supervisada**.

🔎 **Analogía.** Un detector de mentiras que se ha dejado engañar (el clasificador supervisado envenenado) "sabe" exactamente cuál es la pista falsa en la que confía. Si le preguntas "¿qué cambio mínimo te haría dudar más?", te señala la pista falsa; entonces la borras de los ejemplos con los que entrenas al alumno honesto (el SSL).

✅ **Técnico.** Arquitectura con encoder f (ResNet18), projector g (MLP de 3 capas) y clasificador lineal c. Para cada imagen se generan dos vistas; sobre la primera se calcula δ* = argmax L_CE(c(f(x+δ)), y) con ‖δ‖∞ ≤ 4/255 (PGD de 10 pasos de 0.6/255) (Eq. 2). La pérdida es L_VESPR = α·L_CL[g(f(x+δ*)), g(f(x⁺)), g(f(x⁻))] + β·L_CE[c(f(x+δ*)), y] con α = β = 0.5 y L_CL = InfoNCE de SimCLR (temperatura 0.2) (Eq. 3; apéndice B.3).

#### P7.3 Funcionamiento técnico (por qué debería funcionar)
- **[Demuestra]** (Fig. 1) Con un veneno AP, el modelo SL alcanza ~100 % en datos envenenados y ~10 % en limpios; su similitud entre representaciones limpia/envenenada cae a ~0.80; en SSL se mantiene ~0.97 pero aún hay brecha (clean ~0.49 vs poison ~0.66).
- **[Demuestra]** (Tabla 1, promedio de 7 venenos) El objetivo SSL —no las augmentations de SSL— es lo que reduce la similitud intra-clase de imágenes envenenadas (SL 0.78, SL con augmentations SSL 0.79, SSL 0.47) y aumenta la diversidad de features (*E-Rank*: 16.67, 16.43, 59.61).
- **Hipótesis:** "una vez procesados por un encoder envenenado, los venenos ya no son sutiles": el gradiente supervisado apunta a ellos.

#### P7.4 Datos y resultado
- **Entrada:** dataset **etiquetado** envenenado.
- **Salida:** encoder + clasificador entrenados conjuntamente (la evaluación usa directamente el clasificador de VESPR).

#### P7.5 Evaluación
ImageNet-100 (130 000 train / 5 000 test; AP8, CP8 y UE8 generados sobre un subconjunto del 20 %, 26 000 imágenes) y CIFAR-10. Venenos: AP8, CP8, CUDA, LSP16, OPS, RUE8, UE8. Defensas comparadas: SL+AT, Cutout, Mixup, CutMix, ISS, SSL (SimCLR). Métricas: exactitud limpia, **Psn Min** (peor veneno) y **Psn Avg**.

#### P7.6 Resultados clave e interpretación

**Tabla P7-a (P7, Tabla 2; ImageNet-100, exactitud limpia %):**

| Veneno | SL | SL+AT | Cutout | Mixup | CutMix | ISS | SSL | **VESPR** |
|---|---|---|---|---|---|---|---|---|
| Limpio | 77.66 | 69.76 | 78.04 | 80.38 | 81.08 | 71.58 | 70.96 | 74.84 |
| AP8 | 8.18 | 42.88 | 7.68 | 9.68 | 8.38 | 24.22 | 41.80 | **48.68** |
| CP8 | 57.46 | 48.76 | 58.20 | 61.76 | **62.28** | 50.18 | 14.80 | 54.40 |
| CUDA | 6.10 | 36.34 | 8.66 | 5.16 | 5.70 | 3.58 | 26.12 | **45.20** |
| LSP16 | 6.62 | 28.92 | 5.06 | 5.50 | 7.84 | 33.70 | **62.06** | 56.34 |
| OPS | 51.00 | 52.56 | 53.86 | 48.82 | **65.12** | 57.36 | 62.22 | 63.26 |
| RUE8 | 14.18 | 57.34 | 15.60 | 33.08 | 16.10 | 67.52 | 65.30 | **70.36** |
| UE8 | 6.32 | 43.26 | 6.42 | 12.38 | 5.80 | 41.18 | **50.02** | 49.34 |
| **Psn Min** | 6.10 | 28.92 | 5.06 | 5.16 | 5.70 | 3.58 | 14.80 | **45.20** |
| **Psn Avg** | 21.41 | 44.29 | 22.21 | 25.20 | 24.46 | 39.68 | 46.05 | **55.37** |

En CIFAR-10 (Tabla 3): Psn Min 80.31 % (SSL 66.58 %) y Psn Avg 86.74 % (SSL 81.03 %), con limpio 89.35 %.

**Ablaciones relevantes:**
- Tabla 4: SSL+SL (sin AT) Psn Min 21.82 %; SSL+AT 31.78 %; VESPR 45.20 % → **la AT guiada por SL es la pieza clave**.
- Tabla 6: ruido gaussiano en vez de AT (SSL+SL+GN) Min 24.02 %; AT con pérdida SSL (VESPR-SSL) 36.64 %; con ambas (VESPR-Both) 41.26 %; VESPR (pérdida SL) 45.20 %.
- Tabla 13: si al encoder de VESPR se le entrena un **nuevo** clasificador lineal sobre datos envenenados, CUDA cae a 20.50 % → el clasificador conjunto es imprescindible.
- Tabla 14: VESPR funciona con SimCLR, MoCo, SimSiam y BYOL (Min 44.88–48.50 %); con BYOL CUDA llega a 50.14 %.

**Interpretación.**
- **[Demuestra]** VESPR mejora sobre todo el **peor caso** (+16 puntos vs SL+AT) y el promedio (+9 vs SSL) en ImageNet-100.
- **[Demuestra]** No es la mejor en todos los venenos: SSL gana en LSP16 y UE8, CutMix en CP8 y OPS.
- **[Inferencia]** Aun así, la exactitud con veneno sigue lejos de la limpia (55.37 % promedio vs 74.84 %), y VESPR sacrifica algo de exactitud limpia frente a SL (74.84 vs 77.66).
- **[Inferencia]** CP8 (diseñado contra SSL) casi no afecta a SL (57.46 %), y los venenos para SL casi no afectan a SSL en CIFAR-10: es la observación que motiva a **P10 (IRP)**, del mismo grupo.

#### P7.7 Caso aplicado
*Un laboratorio entrena un clasificador médico de 100 clases con imágenes descargadas de varios repositorios, sin saber cuál está envenenado ni con qué método.* Si elige SL+AT, en el peor escenario obtiene ~29 %; con SSL, ~15 % si alguien usó CP; con VESPR, al menos ~45 % frente a cualquiera de los 7 venenos probados. **Para el laboratorio, VESPR es una "póliza de seguro" contra el peor caso**, a costa de un entrenamiento más pesado (AT dentro de SSL de 200 épocas) y de ~3 puntos de exactitud limpia.

#### P7.8 Fortalezas y limitaciones
- **Fortalezas:** evalúa en ImageNet-100 (escenario más realista); reporta peor caso; ablaciones extensas sobre arquitectura, tipo de perturbación, clasificador y método SSL.
- **Limitaciones inferidas:** requiere etiquetas; costo computacional no reportado; un solo backbone (ResNet18); sin varianza; no hay ataque adaptativo diseñado contra VESPR; venenos de ImageNet-100 generados en datasets de tamaño distinto (20 % vs 100 %); no evalúa IRP (P10), del mismo grupo.

#### P7.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "SSL often performs poorly … far below training on clean data"; VESPR "surpasses six previous defenses". |
| **Demuestra** | Tablas 2–6, 12–14. |
| **Inferencia** | VESPR es la mejor póliza de peor caso, no la mejor en cada veneno; brecha con limpio persistente. |
| **No especificado** | Tiempo de entrenamiento; robustez ante atacante adaptativo. |

#### P7.10 Relación con los demás papers
- Mismo grupo y mismos números base que **P10 (IRP)** (las columnas SL, SL+AT, Cutout, Mixup, CutMix, ISS de ImageNet-100 coinciden exactamente). **[Inferencia]** P7 y P10 son las dos caras de una misma investigación: P10 crea una protección que funciona en SL *y* SSL; P7 una defensa que combina SL *y* SSL.
- Reafirma (como **P5**) que **AT** es una de las defensas más sólidas, pero muestra que en ImageNet-100 su peor caso es pobre.
- Su análisis "SSL aprende venenos" contradice la intuición que usa **P5** al proponer augmentations fuertes "de contrastive learning" — aunque P5 las usa dentro de entrenamiento supervisado.

---

### P8 · *GUE: Game-Theoretic Unlearnable Example Generator*

**Autores:** Shuang Liu, Yihan Wang, Xiao-Shan Gao (AMSS/CAS). **Publicación:** P1 lo cita como AAAI 2024. **Rol:** protección con fundamento en teoría de juegos. **Código:** github (hong-xian/gue).

#### P8.1 Problema investigado
La formulación binivel de los unlearnable examples es intratable para redes profundas, y los métodos existentes son mayormente empíricos. Además, P8 identifica tres obstáculos prácticos: (i) **dependencia de una proporción de envenenamiento altísima** (si llegan datos limpios nuevos, la protección se diluye), (ii) **ineficiencia** para proteger datos nuevos y (iii) **vulnerabilidad a AT** (P8, "Challenges").

#### P8.2 Propuesta
1. Modelar la protección como un **juego de Stackelberg no de suma cero** y probar que tiene **equilibrio**.
2. **GUE**: entrenar un **generador** (U-Net tipo autoencoder) que produce la perturbación con una sola pasada hacia adelante, también para datos nuevos.
3. Una **pérdida sustituta** acotada, L_sur.

🔎 **Analogía (juego de Stackelberg).** Un ajedrecista "líder" mueve primero sabiendo que el rival responderá de la mejor forma posible; el líder elige su jugada anticipando esa respuesta.

✅ **Técnico.** El **líder** es el protector (elige la perturbación 𝒜(x, y) con ‖𝒜‖∞ ≤ ε); el **seguidor** es el entrenador (elige θ minimizando el riesgo empírico sobre datos perturbados, J_c). Como las redes llegan a óptimos locales, se usa la **mejor respuesta η-aproximada** BR_η(𝒜). El pago del protector es J_a = sup_{θ∈BR_η} {−E_D[L_a(x, y; θ)]} (pesimista: asume la mejor respuesta del entrenador que más le conviene al entrenador) (P8 Eq. 1–2). "No de suma cero" significa que lo que gana uno no es exactamente lo que pierde el otro: el entrenador minimiza el error en datos envenenados, el protector maximiza el error en datos limpios.

#### P8.3 Funcionamiento técnico
- **Existencia del equilibrio (Teorema 3):** si el espacio de parámetros Θ es compacto (Assumption 1) y las pérdidas son continuas (Assumption 2), existe un equilibrio de Stackelberg. La prueba usa semicontinuidad inferior de la "función marginal" y compacidad (sección "Proofs").
- **Problema con cross-entropy:** con L_a = CE el equilibrio existe (Corolario 4), pero CE no está acotada → gradientes que explotan.
- **L_sur(x, y; θ) = −max_{k≠y} L_ce(x, k; θ)** (Eq. 3). 🔎 *Analogía:* en lugar de "alejarse lo máximo de la respuesta correcta" (sin techo), "acercarse a *alguna* respuesta incorrecta" (con techo). ✅ *Técnico:* L_sur ≤ −log(K−1) y L_ce crece cuando L_sur crece (Proposición 5); su maximización crea un "valle" en la etiqueta verdadera y vuelve uniformes las demás.
- **Escenario adversarial:** se reemplaza L_c por la pérdida de AT (Madry) o **TRADES**; también hay equilibrio (Lema 7, Corolario 8).
- **Garantía de "ataque más poderoso"** (Remark 9 y Corolario 17): con una pérdida discreta basada en **Carlini–Wagner** (L_acc), el equilibrio da la menor exactitud posible dentro del espacio de hipótesis. **[Inferencia]** Esa garantía se demuestra para L_acc, **no** para la L_sur que se usa en la práctica.
- **Cómo se resuelve:** el juego se reduce a una binivel (Eq. 5) y se resuelve con **BOME** (*first-order bilevel optimization*): reemplaza el problema interior por la restricción q(w,θ) = h(w,θ) − h(w, θ^T(w)) ≤ 0, aproximando θ^T con T = 10 pasos de descenso, y lo resuelve con **dynamic barrier gradient descent** (λ_k adaptativo, ρ = 1.5). Así evita calcular matrices Hessianas (Alg. 1).

#### P8.4 Datos y resultado
- **Entrada:** dataset etiquetado (o una fracción) para entrenar el generador g_w; ε = 8/255.
- **Salida:** un generador que produce perturbaciones *sample-wise* con salida ε·tanh(·), reutilizable en datos nuevos.

#### P8.5 Evaluación
CIFAR-10 y CIFAR-100; víctima ResNet-18 (más VGG16, ResNet-50, DenseNet-121, WRN-28-10 para transferencia); generación GUE 50 épocas (GUE-AT 150); evaluación 100 épocas.

#### P8.6 Resultados clave e interpretación

**Tabla P8-a (P8, Tablas 1, 3, 4; ResNet-18, exactitud limpia %):**

| Escenario | Limpio | EM | TAP | DeepConfuse | ShortcutGen | **GUE** |
|---|---|---|---|---|---|---|
| CIFAR-10 (ERM) | 92.36 | **10.16** | 20.28 | 22.74 | 24.42 | 13.25 |
| CIFAR-100 (ERM) | 70.59 | **1.90** | 15.40 | 25.73 | 8.62 | 8.35 |

| Defensa vs GUE (CIFAR-10) | Mixup | Cutout | CutMix | AT 2/255 | AT 4/255 |
|---|---|---|---|---|---|
| Exactitud | 11.83 | 14.71 | 19.84 | 22.55 | **76.96** |

| AT radio ε_d | 0 | 2/255 | 4/255 |
|---|---|---|---|
| Limpio | 92.36 | 92.41 | 89.82 |
| INF | 88.78 | 80.91 | 77.96 |
| REM(8-2) | 21.14 | 29.75 | 74.20 |
| **GUE-AT(8-2)** | **16.11** | **22.27** | 60.11 |
| REM(8-4) | 26.15 | 30.90 | **44.91** |
| GUE-AT(8-4) | 17.86 | 21.19 | 52.79 |

- **Generalización (Fig. 3, CIFAR-10):** generador entrenado con 100 % → ~13 %; 60 % → ~24 %; 50 % → ~41 %; 20 % → ~91 % (ya no protege). DeepConfuse y ShortcutGen con 60 %: ~67 % y ~85 %.
- **Transferencia (Tabla 2):** 12.97–14.68 % en cinco arquitecturas.
- **Ablación (Fig. 5):** con CE la norma del gradiente es ~2 órdenes mayor y J_c no converge (ni con *gradient clipping*); con L_sur sí converge.

**Interpretación.**
- **[Demuestra]** GUE es el segundo mejor bajo ERM (detrás de EM) y no muestra el "pico" de exactitud al inicio del entrenamiento que sí muestran otros (Fig. 2).
- **[Demuestra]** Su principal aporte práctico es que **un generador entrenado con parte de los datos protege datos que no vio**, mucho mejor que DeepConfuse/ShortcutGen.
- **[Demuestra]** GUE estándar **no resiste AT 4/255** (76.96 %). GUE-AT mejora sobre REM con ε_d = 2/255, pero con ε_d = 4/255 **REM(8-4) es mejor que GUE-AT(8-4)** (44.91 vs 52.79). **[Inferencia]** El texto solo destaca el caso favorable (2/255).
- **[Inferencia]** "Generalización" aquí significa *entrenar el generador con una fracción y aplicarlo a todo el dataset*; no es lo mismo que la **protección parcial** de P2/P9/P10 (mezclar datos protegidos con limpios), que P8 no evalúa.

#### P8.7 Caso aplicado
*Una agencia de fotos sube 1 000 imágenes nuevas cada día.* Con EM tendría que re-optimizar perturbaciones cada vez; con GUE entrena el generador una vez (con, digamos, el 60–90 % de su archivo) y protege cada imagen nueva con una pasada de la U-Net. Un comprador que entrene normalmente obtendría ~13–24 % (análogo CIFAR-10). **Pero** si el comprador aplica AT de 4/255, el GUE estándar deja ~77 %; la agencia debería usar GUE-AT y aun así el comprador obtendría ~53–60 %.

#### P8.8 Fortalezas y limitaciones
- **Fortalezas:** marco teórico unificador (normal y adversarial); existencia demostrada; algoritmo de primer orden; generador reutilizable; ablación que justifica L_sur.
- **Limitación declarada:** el algoritmo no converge bien con radios de AT grandes.
- **Limitaciones inferidas:** solo CIFAR (32×32); costo de generación no reportado en el md (P9 reporta 3.75 h CIFAR-10 / 10.2 h ImageNet-100 para GUE); sin evaluación contra ISS, purificación o detección; existencia ≠ que BOME alcance ese equilibrio.

#### P8.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "El equilibrio da el ataque más poderoso"; GUE "works by using a relatively small percentage of the training data". |
| **Demuestra** | Teorema 3, Corolarios 4, 6, 8, 17 (pruebas en el md); Tablas 1–4; Figs. 2–5. |
| **Inferencia** | Garantía de optimalidad solo para L_acc; GUE-AT no domina a REM en 4/255. |
| **No especificado** | Tiempo de generación; resultados en alta resolución. |

#### P8.10 Relación con los demás papers
- Mismo grupo que **P5**; ambos usan herramientas teóricas (separabilidad, equilibrio).
- Es la única protección **sample-wise basada en generador** del bloque; las demás son *class-wise* (CUDA, EUN, IRP, KBS, P13) o *per-image* optimizadas (PyrFawkes).
- **P9 (KBS)** lo usa como baseline (CIFAR-10 AT: GUE 52.79 % vs KBS 20.97 %), y la cifra coincide con GUE-AT(8-4) bajo ε_d = 4/255.

---

### P9 · *KBS: K-Space Bispectrum Steganography for Robust Unlearnable Data*

**Autores:** Jiahao Li, Yiqiang Chen, Yunbing Xing, Yang Gu, Xiangyuan Lan (ICT-CAS, Pengcheng Lab). **Publicación:** ACM MM '25. **Rol:** protección *surrogate-free*.

#### P9.1 Problema investigado
CUDA (P2) demostró que se puede proteger sin modelo sustituto, pero según P9 tiene tres problemas: (1) **pérdida de detalle de alta frecuencia** y *border effects* (bordes oscuros); (2) **"hash collisions"**: con un kernel de tamaño fijo hay pocos patrones distintos (ej.: un kernel 9×9 → 81 patrones), así que con muchas clases varias comparten patrón; (3) vulnerabilidad al ataque **RSKFF** (*sharpening* + *frequency filtering*) (P9 §1, §2.2).

#### P9.2 Propuesta
**KBS**: esconder un **código binario por clase** en la **magnitud y la fase** del espectro de Fourier de cada imagen ("k-space bispectrum" en la terminología del paper), mediante redes entrenadas.

- **Esteganografía** — 🔎 *Analogía:* escribir un mensaje con tinta invisible dentro de una carta normal. ✅ *Técnico:* ocultar información en un portador sin que se note su existencia (P9 §2.3).
- **Dominio de Fourier / k-space; magnitud y fase** — 🔎 *Analogía:* descomponer una canción en "cuánto suena cada nota" (magnitud) y "en qué momento empieza cada nota" (fase). ✅ *Técnico:* la FFT expresa la imagen como F(x) = M(x)·e^{jP(x)} (Eq. 2), donde M es la magnitud y P la fase de cada frecuencia espacial.

#### P9.3 Funcionamiento técnico
1. Cada clase c recibe un código b ∈ {0,1}^k con k = ⌈log₂ C⌉ (10 clases → 4 bits; 100 clases → 7 bits).
2. Dos generadores producen δ_M = ζ(b) y δ_P = ξ(b) (Sección 4.2).
3. Se concatenan a M(x) y P(x) por canal y dos **autoencoders** g, φ reconstruyen M_δ y P_δ (Eq. 3–4).
4. x' = iFFT(M_δ·e^{jP_δ}) (Eq. 5–6).
5. Tres **extractores** Ψ_α (desde la imagen x'), Ψ_β (desde M_δ) y Ψ_γ (desde P_δ) deben recuperar b (Eq. 12).
6. **Pérdidas:** L_recon = promedio de L_spatial (‖x'−x‖₁), L_magnitude, L_phase y L_freq (diferencias en parte real e imaginaria) (Eq. 7–11); L_stego = BCE promedio de los tres extractores (Eq. 13–14); **L = L_recon + λ·L_stego**, λ = 1 (Eq. 15).

**[Inferencia]** La lógica de la protección: el entrenamiento obliga a que la etiqueta (el código) sea **fácil de leer desde la imagen** (un atajo por clase), mientras la reconstrucción mantiene la imagen visualmente igual. No hay clasificador sustituto: el "atajo" se diseña explícitamente como un mensaje oculto.

#### P9.4 Datos y resultado
- **Entrada:** dataset etiquetado; se entrenan los autoencoders/generadores/extractores (AdamW, lr 1e-3, batch 512, 100 épocas).
- **Salida:** dataset protegido x'. **[No especificado]** Las arquitecturas de autoencoders, generadores y extractores están en material suplementario no incluido en el md.

#### P9.5 Evaluación
CIFAR-10, CIFAR-100, ImageNet-100; ResNet-18, VGG-16, WRN-34-10, DenseNet-121; baselines TAP, NTGA, EM, REM, SEM, PUE, GUE, CUDA; AT ℓ∞ 4/255 por defecto; RTX 3090.

#### P9.6 Resultados clave e interpretación

**Tabla P9-a (P9, Tabla 1; ResNet-18, exactitud limpia %; menor = mejor):**

| Dataset | Entren. | Limpio | TAP | NTGA | EM | REM | SEM | PUE | GUE | CUDA | **KBS** |
|---|---|---|---|---|---|---|---|---|---|---|---|
| CIFAR-10 | ERM | 94.66 | 22.51 | 16.27 | 13.20 | 27.09 | **13.00** | 17.39 | 13.25 | 18.48 | 13.53 |
| CIFAR-10 | AT | 89.51 | 88.02 | 88.96 | 88.62 | 48.16 | 31.92 | 84.54 | 52.79 | 44.40 | **20.97** |
| CIFAR-100 | ERM | 76.27 | 13.75 | 3.22 | **1.60** | 10.14 | 1.95 | 3.40 | 8.35 | 12.69 | 1.82 |
| CIFAR-100 | AT | 64.50 | 62.39 | 62.44 | 63.43 | 27.10 | 20.25 | 62.71 | 43.02 | 34.34 | **18.04** |
| ImageNet-100 | ERM | 80.66 | 9.10 | 8.42 | **1.26** | 13.74 | 4.10 | 3.87 | 11.54 | 8.96 | 3.46 |
| ImageNet-100 | AT | 66.62 | 63.56 | 63.06 | 63.40 | 41.66 | 37.82 | 65.33 | 38.90 | 38.68 | **26.04** |

**Tabla P9-b — CUDA vs KBS con distintos AT (P9, Tabla 4, CIFAR-10):**

| | ERM | L∞ 4/255 | L∞ 8/255 | L∞ 16/255 | L2 0.25 | L2 0.50 | L2 0.75 |
|---|---|---|---|---|---|---|---|
| CUDA | 18.48 | 44.40 | 32.85 | **19.32** | 39.05 | 51.19 | 51.14 |
| KBS | **13.53** | **20.97** | **25.51** | 27.46 | **14.35** | **23.89** | **27.79** |

**Tabla P9-c — Ataque RSKFF (P9, Tabla 5):**

| | CIFAR-10 | CIFAR-100 | ImageNet-100 |
|---|---|---|---|
| CUDA bajo RSKFF | 78.53 | 53.33 | 43.08 |
| KBS bajo RSKFF | **42.53** | **11.13** | **25.48** |

**Otros:** augmentations en CIFAR-10 (AutoAugment 10.58 %, orthogonal reg. 11.03 %, Mixup 15.57 %, Cutout 16.66 %, CutMix 16.17 %, JPEG 16.82 %, Randomized Smoothing 16.19 %, **Grayscale 35.24 %**); protección parcial (Tabla 3, AT): 80 % protegido → 82.74 % (CUDA 84.34 %); ERM 80 % → 74.52 % (CUDA 85.61 %); tiempo (Tabla 8): 0.25 h CIFAR-10 y 2.65 h ImageNet-100 (CUDA 10.8 s y 0.15 h; REM 22.6 h y 51.2 h).

**Ablación de extractores (Tabla 6, bajo AT):** sin el extractor espacial Ψ_α la protección colapsa (CIFAR-10 78.72 %); los tres juntos dan 20.97 %. **Sensibilidad a λ (Tabla 7):** 0.01 → 42.87 %; 1.0 → 20.97 %; 10 → 11.73 % pero con artefactos visibles (Fig. 4).

**Interpretación.**
- **[Demuestra]** KBS es claramente la protección más fuerte bajo **AT 4/255** en las tres bases y bajo **RSKFF**.
- **[Demuestra]** Bajo ERM no es la mejor: EM y SEM le igualan o superan.
- **[Inferencia]** Contradicciones internas: (a) el texto dice "KBS consistently achieves lower clean test accuracy compared to CUDA across all training settings", pero con AT L∞ 16/255 en CIFAR-10 KBS es peor (27.46 vs 19.32); (b) atribuye "16.32 %" a un presupuesto de 16/255, pero en la tabla 16.32 corresponde a CIFAR-100 con 8/255; (c) dice que KBS es "consistentemente el más bajo" en arquitecturas, pero en WRN-34-10 **SEM** es menor (31.42 vs 43.80; Tabla 2); (d) la Tabla 2 no indica que sea bajo AT, aunque sus valores coinciden con los de AT.
- **[Inferencia]** La sensibilidad a **grayscale** (35.24 %) es mayor que la que P2 reporta para CUDA (20.12 %), aunque con setups posiblemente distintos.
- **[Demuestra]** Con protección parcial y ERM, KBS sí reduce algo más que CUDA (80 % → 74.52 vs 85.61), pero sigue lejos de proteger.

#### P9.7 Caso aplicado
*Un banco de imágenes médicas de 100 categorías quiere publicar un dataset de muestra que no sirva para entrenar.* Con CUDA, el patrón visual (bordes oscuros, blur) es evidente y un adversario con RSKFF recupera ~43 % (ImageNet-100). Con KBS, las imágenes conservan los detalles finos y el adversario con RSKFF obtiene ~25 %, o ~26 % con AT. **Pero** el banco debe entrenar autoencoders (2.65 h), no tiene métricas cuantitativas de calidad que respalden la "imperceptibilidad", y si el adversario convierte a escala de grises (en el análogo CIFAR-10) recupera ~35 %.

#### P9.8 Fortalezas y limitaciones
- **Fortalezas:** escalable a muchas clases (código binario); fuerte bajo AT y RSKFF; ablaciones de cada extractor y de λ; tiempos reportados.
- **Limitaciones inferidas:** sin PSNR/SSIM/LPIPS; sin presupuesto ε para KBS aunque el planteamiento del problema lo define; baselines aparentemente tomados de la literatura pese a afirmar "identical settings"; no evaluado contra EPD/COIN (P4), detección (P5), difusión, ni atacante adaptativo que conozca el pipeline; contradicciones texto–tabla señaladas arriba.

#### P9.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Preserva alta frecuencia", "evita hash collisions", "resiste ataques de frecuencia", "imperceptible en el espacio de píxeles". |
| **Demuestra** | Tablas 1–8 (con las excepciones mencionadas); visualización Fig. 3 (cualitativa). |
| **Inferencia** | Ventaja clara bajo AT 4/255 y RSKFF; no uniforme en otros escenarios; imperceptibilidad sin cuantificar. |
| **No especificado** | Arquitecturas de las redes; presupuesto de perturbación; calidad visual medida. |

#### P9.10 Relación con los demás papers
- **Rival directo de P2 (CUDA)** y de **P10 (IRP)**: los tres son *surrogate-free* por clase, pero KBS trabaja en Fourier con redes entrenadas, IRP con filtros espaciales restaurados y CUDA con filtros aleatorios.
- Comparte con **P13** la idea de "código oculto en la imagen" (esteganografía en KBS, marca de agua en P13), pero en KBS el código *es* la protección, mientras en P13 la marca de agua sirve para verificar autoría y la protección es otra perturbación.
- Usa a **GUE (P8)** como baseline.

---

### P10 · *IRP: Leveraging Imperfect Restoration for Data Availability Attack*

**Autores:** Yi Huang, Jeremy Styborski, Mingzhi Lyu, Fan Wang, Adams Kong (NTU Singapur; mismo grupo que P7). **Rol:** protección (DAA) efectiva en SL y SSL. **Código:** github (lyumingzhi/IRP).

#### P10.1 Problema investigado
Los DAAs suelen funcionar en **SL** *o* en **SSL**, no en ambos. En CIFAR-10 (Fig. 1) casi todos los venenos de SL apenas afectan al SSL (EM ~88 %, LSP ~89 %), y CP (para SSL) casi no afecta al SL (~94 %). **CUDA** es el único razonable en ambos, pero en SSL deja ~66–70 % y para ser más fuerte necesita kernels grandes que **difuminan visiblemente** la imagen (Fig. 2) (P10 §1).

#### P10.2 Propuesta
1. Un **análisis teórico de CUDA en redes profundas** (no en GMM como P2).
2. **IRP** (*Imperfect Restoration Poisoning*): intentar "des-difuminar" la imagen CUDA con un filtro lineal aprendido por clase; como la restauración **no puede ser perfecta**, queda una imagen nítida que aún lleva un sesgo por clase.

🔎 **Analogía.** Tomas una foto con un lente sucio (CUDA) y luego intentas limpiarla con un programa de "enfoque" hecho a medida para ese lente. La foto queda casi nítida, pero el programa deja una "firma" sutil y distinta para cada lente; la red aprende esa firma.

✅ **Técnico.** Ver §P10.3.

#### P10.3 Funcionamiento técnico
- **Análisis 1 — gradiente no óptimo (§3.3):** el gradiente del primer filtro W con datos CUDA, Σ(Xᵢ⋆Rᵢ)⋆∂f/∂u, difiere del gradiente óptimo con datos limpios; para tasas de aprendizaje pequeñas eso aumenta la pérdida sobre datos limpios.
- **Análisis 2 — sesgo por clase (§3.4, Teorema 1):** la salida de la primera capa depende de R_cπ ⋆ R_iπ (correlación entre el filtro de la clase c y el de otra clase i). Si 1 > (κ²−2)p_u² + 2p_u: la **autocorrelación** de un filtro tiene su pico en el centro; la **correlación cruzada** entre filtros de clases distintas tiene el pico **desplazado** (probabilidad de estar en el centro 1/κ²; para κ = 9, el desplazamiento ocurre con probabilidad 0.988). Consecuencia: la información de la propia clase llega "en su sitio" a capas superiores y la de otras clases llega desplazada → **sesgo por clase**.
- **IRP (§3.5):** (1) aplicar el filtro CUDA R_c a todas las imágenes de la clase c; (2) cortar parches κ×κ; (3) ajustar una **regresión lineal por clase** α_c que, con MSE, prediga el píxel original del centro de cada parche (Eq. 6); (4) A_c = α_c reorganizado como filtro; (5) filtro final **P_c = R_c ⋆ A_c**; (6) imagen protegida X ⋆ P_c. **Teorema 2:** la restauración perfecta es imposible si el filtro tiene tamaño > 1. Los filtros P_c tienen patrones "turbulentos" de alta frecuencia alrededor del pico (Fig. 4).

#### P10.4 Datos y resultado
- **Entrada:** imágenes limpias etiquetadas (necesarias para ajustar α_c); parámetros de CUDA.
- **Salida:** dataset protegido más nítido que CUDA.

#### P10.5 Evaluación
CIFAR-10, CIFAR-100, STL-10, ImageNet-100; ResNet-18 (también ResNet-34, VGG-19, DenseNet-121, MobileNet-V2, ViT-S); SSL con SimCLR, SimSiam, MoCoV3, BYOL (evaluación por linear probing). 8 baselines: EM, TAP, REM, CP, CUDA, LSP, OPS, AR. Defensas: AT (L∞ 4/255, PGD-10, paso 0.6/255), ISS (grayscale + JPEG-10), Cutout, CutMix, Mixup; en apéndice AVATAR, UEraser y **COIN**. Columna **Max** = peor caso para el protector.

#### P10.6 Resultados clave e interpretación

**Tabla P10-a (P10, Tabla 1; CIFAR-10, exactitud limpia %; menor = mejor):**

| DAA | SL | SSL | AT | Cutout | CutMix | Mixup | ISS | **Max** |
|---|---|---|---|---|---|---|---|---|
| Limpio | 93.86 | 90.55 | 89.57 | 95.62 | 95.78 | 95.46 | 82.40 | 95.62 |
| EM | 32.53 | 88.45 | 89.31 | 36.18 | 38.40 | 54.19 | 82.12 | 89.31 |
| REM | 18.91 | 86.70 | 34.45 | 19.67 | 27.46 | 23.27 | 80.04 | 86.70 |
| AP | 12.45 | 75.77 | 85.67 | 9.70 | 8.38 | 33.27 | 81.10 | 85.67 |
| CP | 93.59 | 73.08 | 88.43 | 94.51 | 93.77 | 93.36 | 82.21 | 94.51 |
| LSP | 21.47 | 88.87 | 85.67 | 18.47 | 21.68 | 22.76 | 79.33 | 88.87 |
| OPS | 27.24 | 87.79 | 20.10 | 65.12 | 85.27 | 37.56 | 72.14 | 87.79 |
| AR | 10.95 | 86.86 | 72.94 | 12.17 | 12.95 | 14.15 | 82.83 | 86.86 |
| CUDA | 21.89 | 66.58 | 48.58 | 23.46 | 24.04 | 21.75 | **21.94** | 66.58 |
| **IRP** | **10.39** | **43.24** | **32.21** | **10.85** | **15.21** | **16.03** | 29.42 | **43.24** |

**Tabla P10-b (P10, Tabla 2; ImageNet-100):** Max de IRP 22.10 % (AT) frente a 36.34 % de CUDA; IRP en SL 1.98 %, SSL 9.30 %, ISS 4.14 % (CUDA ISS 3.58 %).

**Calidad de imagen (P10, Tabla 6, ImageNet-100):**

| Métrica | CUDA | IRP | Limpio |
|---|---|---|---|
| LPIPS ↓ | 0.272 | **0.142** | — |
| SSIM ↑ | 0.561 | **0.775** | — |
| MS-SSIM ↑ | 0.831 | **0.903** | — |
| CLIP-IQA ↑ | 0.528 | **0.706** | 0.787 |
| BRISQUE ↓ | 29.570 | **17.318** | 16.625 |

(**LPIPS**: distancia perceptual aprendida; **SSIM/MS-SSIM**: similitud estructural; **CLIP-IQA** y **BRISQUE**: calidad sin referencia.)

**Defensas recientes (P10, Tabla 8, CIFAR-10):** AVATAR CUDA 55.93 / IRP 54.78; UEraser 40.56 / 26.55; **COIN 71.90 / 53.32**.

**Otros:** 4 métodos SSL en ImageNet-100: 7.82–11.6 % (Tabla 3); transferencia con AT a 6 arquitecturas: peor caso CIFAR-10 35.39 % (CUDA 49.44 %), CIFAR-100 28.85 % (37.95 %), STL-10 35.58 % (45.99 %) (Tabla 5); filtros de igual tamaño pero sin restauración (*Random Sharpness*, *Double Blur*) no igualan a IRP (Fig. 6).

**Interpretación.**
- **[Demuestra]** IRP es la protección con mejor **peor caso** entre los 9 DAAs evaluados, en CIFAR-10 e ImageNet-100, y además con mejor calidad visual que CUDA.
- **[Demuestra]** No gana *todas* las celdas: bajo ISS, CUDA es algo mejor (CIFAR-10 21.94 vs 29.42; ImageNet-100 3.58 vs 4.14); en CIFAR-100 con DenseNet121 y MobileNetV2 bajo AT, CUDA también es mejor (Tabla 5). **[Inferencia]** La frase "IRP outperforms baseline poisons by a large margin in all cases" es una sobre-generalización leve.
- **[Demuestra]** Con protección parcial (Tabla 4), IRP (y todos los DAAs) pierde efecto: 80 % protegido → 85.70 % vs 77.36 % entrenando solo con el 20 % limpio. Los autores interpretan que los DAAs no "fallan", sino que los datos protegidos simplemente no aportan.
- **[Demuestra]** Frente a COIN (la defensa de P4), IRP resiste mejor que CUDA (53.32 vs 71.90), pero AVATAR (difusión) lleva a IRP a 54.78 %.

#### P10.7 Caso aplicado
*Una ilustradora publica su portafolio y teme que se use para entrenar modelos, ya sea con aprendizaje supervisado o auto-supervisado.* Con CUDA sus imágenes se verían borrosas (SSIM 0.561) y un modelo SSL aún aprendería ~66 %; con IRP se ven casi nítidas (SSIM 0.775) y el peor caso baja a ~43 % (CIFAR-10) o ~22 % (ImageNet-100). **Pero** si un adversario aplica difusión (AVATAR) o COIN recupera ~53–55 %, y si la ilustradora solo protege parte de su obra, el resto basta para entrenar.

#### P10.8 Fortalezas y limitaciones
- **Fortalezas:** teoría sobre redes profundas (primer capa) que explica CUDA; métrica de peor caso; SL + SSL + 5 defensas + 3 defensas recientes; métricas cuantitativas de calidad; ablación del tamaño de filtro.
- **Limitaciones inferidas:** teoría solo sobre la primera capa y para tasa de aprendizaje pequeña; requiere datos limpios por clase para ajustar α_c; sin presupuesto de norma; protección parcial débil; no evaluado contra detección (P5) ni EPD (P4); sin varianza en el md.

#### P10.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "IRP significantly outperforms previous DAAs" en todos los casos. |
| **Demuestra** | Teoremas 1–2 (pruebas en apéndice A); Tablas 1–8; Fig. 6. |
| **Inferencia** | Mejor peor caso, pero no mejor en cada celda; COIN/AVATAR siguen siendo amenazas relevantes. |
| **No especificado** | Tiempo de generación; varianza; detectabilidad por bordes. |

#### P10.10 Relación con los demás papers
- **Extiende P2 (CUDA)** y responde a la crítica de P2 sobre su teoría gaussiana.
- **Usa P4 (COIN)** como defensa de evaluación → es el único paper del bloque que prueba una protección contra la defensa diseñada específicamente contra ella.
- **Complementa P7 (VESPR)** (mismo grupo, mismos números base de ImageNet-100). **[Inferencia]** VESPR no evalúa IRP; sería la prueba natural siguiente.
- Comparte con **P9 (KBS)** la preocupación por calidad visual, pero solo P10 la mide.

---

### P11 · *Novel poisoning attacks for clustering methods via robust feature generation*

**Autores:** Chen Zhang, Zhuo Tang (China Univ. of Petroleum; Hunan Univ.). **Publicación:** revista Elsevier. **Rol:** ataque de evasión y de envenenamiento contra **clustering**, presentado también como protección de privacidad ("unlearnable para clustering").

#### P11.1 Problema investigado
Casi toda la literatura de ejemplos adversariales y envenenamiento es supervisada. El **clustering** (agrupar datos sin etiquetas), incluido el incremental o *data-stream*, también puede atacarse, tanto en **tiempo de decisión** (cuando el modelo asigna un dato nuevo a un grupo) como en **tiempo de entrenamiento** (envenenando los datos que se agrupan) (P11 §1).

- **Clustering** — 🔎 *Analogía:* ordenar una caja de botones por parecido sin que nadie te diga las categorías. ✅ *Técnico:* particionar N datos en k grupos; **k-means** minimiza la distancia de cada punto a su centro (Eq. 1); **GMM** modela los datos como mezcla de gaussianas ("soft k-means", Eq. 2–3); **VaDE** es un VAE con prior de mezcla gaussiana (Eq. 4); **Deep k-means** aprende representación y centros a la vez.
- **Evasion attack / ejemplo adversarial en tiempo de decisión** — perturbar una entrada para que el modelo ya entrenado la asigne mal (P11 §2.3.1).
- **ACC no supervisada** — exactitud tras encontrar el mejor emparejamiento uno-a-uno entre clusters y etiquetas reales (Eq. 12). **NMI** (*Normalized Mutual Information*): mide cuánta información comparten clusters y etiquetas (1 = perfecto, 0 = nada).

#### P11.2 Propuesta
1. Ataque de evasión para k-means/GMM en forma cerrada (Alg. 1).
2. Envenenamiento de k-means/GMM moviendo todos los puntos a la frontera.
3. Envenenamiento de deep clustering con **"robust features"** universales por cluster (Alg. 2).

#### P11.3 Funcionamiento técnico
- **Evasión (Eq. 13–19):** entre dos centros mᵢ, mⱼ hay un hiperplano frontera w·x + b = 0 con w = mᵢ − mⱼ. La perturbación L2 mínima para cambiar el cluster es la **proyección ortogonal** de x₀ sobre el hiperplano más cercano (fórmula cerrada Eq. 19). Para GMM se normaliza por la varianza.
- **Envenenamiento k-means/GMM:** entrenar k-means en datos limpios, aplicar Alg. 1 a *todos* los puntos de entrenamiento (quedan sobre las fronteras) y publicar esos datos; un nuevo clustering sobre ellos se confunde.
- **Envenenamiento deep clustering (Fig. 2):**
  1. entrenar el modelo de clustering en datos limpios;
  2. para cada cluster t, optimizar una perturbación **universal** r_t (‖r‖ₚ ≤ ε) tal que f(x + r_t) = t para (casi) cualquier x (Eq. 20); pérdida tipo Carlini–Wagner en el espacio latente: L_t = max(L(h(x+r), C_t) − min_{c≠t} L(h(x+r), C_c), −κ) (Eq. 21; Eq. 22 para VaDE usando logits);
  3. **a cada imagen limpia sumarle un robust feature elegido al azar** (no el de su clase);
  4. la víctima entrena con esos datos y agrupa por el feature, no por el contenido.
  - Alg. 2 usa Adam, mini-batches y se detiene cuando la *strength ratio* sth(r) (fracción de imágenes que el feature arrastra al cluster t) supera δ.

🔎 **Analogía.** Pegar al azar una de diez calcomanías muy llamativas en cada botón: quien ordena los botones los agrupa por calcomanía, no por forma ni color.

✅ **Técnico.** El "robust feature" es una perturbación universal que domina la representación latente; como se asigna aleatoriamente, rompe cualquier correlación entre contenido y grupo.

#### P11.4 Datos y resultado
- **Entrada:** acceso **white-box** al modelo de clustering entrenado (centros o red); todo el dataset de entrenamiento.
- **Salida:** dataset envenenado (o un ejemplo adversarial por entrada).

#### P11.5 Evaluación
Iris (150 puntos, 3 clases), MNIST, Fashion-MNIST, **TIMIT** (voz, 630 hablantes; *speaker diarization* con GMM-UBM) y CIFAR-2 (avión vs auto). Encoder LeNet. ε (L∞) = 0.3 (MNIST), 0.1 (Fashion-MNIST), 0.05 (CIFAR-2). Baselines: Spill-over, Cina's (algoritmo genético), A²SC, DeepConfuse y Alignment (adaptados con pseudo-etiquetas), ruido aleatorio.

#### P11.6 Resultados clave e interpretación

**Tabla P11-a (P11, Tablas 1 y 4; clustering clásico):**

| Dataset / modelo | ACC limpio | NMI limpio | ACC envenenado | NMI envenenado | Test limpio | Test con evasión |
|---|---|---|---|---|---|---|
| Iris / k-means | 89.3 % | 0.775 | 47.3 % | 0.363 | — | — |
| Iris / GMM | 96.7 % | 0.898 | 52.9 % | 0.395 | — | — |
| MNIST / k-means | 52.8 % | 0.501 | 22.5 % | 0.291 | 51.1 % | 0.0 % |
| MNIST / GMM | 53.7 % | 0.525 | 24.0 % | 0.303 | 53.2 % | 0.1 % |
| TIMIT / GMM-UBM | 63.6 % | 0.598 | 1.2 % | 0.171 | 60.4 % | 0.0 % |
| MNIST / SPI-K-means (incremental) | 51.6 % | — | 20.8 % | — | — | 0.0 % |

**Tabla P11-b (P11, Tablas 2 y 7; deep clustering, exactitud de entrenamiento / test):**

| | MNIST VaDE | MNIST Deep k-means | F-MNIST VaDE | F-MNIST Deep k-means | CIFAR-2 VaDE | CIFAR-2 Deep k-means |
|---|---|---|---|---|---|---|
| Limpio | 92.38 / 89.61 | 86.52 / 83.85 | 57.80 / 53.44 | 54.2 / 50.16 | 80.25 / 79.74 | 81.61 / 81.08 |
| Envenenado | **11.25 / 17.51** | **14.98 / 22.70** | **14.36 / 19.33** | **18.62 / 28.96** | **51.01 / 52.8** | **50.8 / 51.56** |

Contra baselines adaptados (VaDE, MNIST / F-MNIST, ACC): ruido aleatorio 90.75 / 52.55 %; DeepConfuse 66.26 / 37.21 %; Alignment 58.93 / 34.44 %; **P11 11.25 / 14.36 %** (Tabla 7).

**Interpretación.**
- **[Demuestra]** El envenenamiento lleva el deep clustering cerca del azar (10 % para 10 clases; 50 % para CIFAR-2) y los ataques de evasión logran casi 100 % de éxito en k-means/GMM.
- **[Inferencia]** Los presupuestos son grandes: ε = 0.3 en MNIST equivale a ~76/255, casi diez veces el típico 8/255 de las protecciones de imagen; el propio texto describe las perturbaciones en MNIST como "small to perceptible".
- **[Inferencia]** Al asignar features aleatorios, el envenenamiento **destruye** la utilidad del dato para cualquier agrupamiento; es más cercano a "ruido dominante" que a un atajo sutil.
- **[Inferencia]** La Tabla 5 (defensa robusta de [17]) reporta para datos envenenados exactamente los mismos valores que la Tabla 2 sin defensa (11.25, 14.98, 14.36, 18.62), sin comentario. La Tabla 8 dice "digits pair 1&2" en el título y "3&2" en el texto.
- **Costo** (Tabla 6 y §4.4): 0.62–1.50 s por ejemplo adversarial en k-means/GMM; 31.2–35.6 s en VaDE/Deep k-means; robust features 5–8 min por categoría; < 2 h en total.

#### P11.7 Caso aplicado
*Una empresa comparte con una consultora datos de clientes (por ejemplo, imágenes de productos comprados) para un servicio de segmentación, pero no quiere que el modelo resultante le sirva a la consultora para otros fines.* Aplicando el método, la segmentación que obtenga la consultora con deep clustering sería casi aleatoria (≈11–19 % en el análogo MNIST/F-MNIST). **Advertencias:** la empresa necesita acceso white-box a un modelo de clustering similar; las perturbaciones pueden ser visibles; y si la empresa quiere que el servicio legítimo funcione, este método no lo permite (no es reversible, a diferencia de P13).

#### P11.8 Fortalezas y limitaciones
- **Fortalezas:** único paper del bloque sobre **aprendizaje no supervisado**; cubre clustering clásico, incremental y profundo; incluye voz (TIMIT); fórmula cerrada para k-means; tiempos reportados.
- **Limitación declarada:** solo white-box.
- **Limitaciones inferidas:** datasets pequeños o simples; ε grandes; exactitud de clustering limpio a veces baja (k-means en MNIST 52.8 %); no evalúa defensas modernas (AT, augmentations, purificación) ni detección; inconsistencias de tablas mencionadas.

#### P11.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Success rate cercano a 100 %"; "el clustering envenenado se comporta como una adivinanza aleatoria"; "preserva la privacidad". |
| **Demuestra** | Tablas 1–8 para los datasets y modelos indicados. |
| **Inferencia** | Presupuestos grandes; imperceptibilidad no garantizada; Tabla 5 sospechosa. |
| **No especificado** | Valores de κ, δ e iteraciones I de Alg. 2; número de repeticiones. |

#### P11.10 Relación con los demás papers
- Comparte con **P2/P6/P10** la idea de un **patrón dominante** que la red aprende en lugar del contenido (aquí, "robust features").
- A diferencia de todas las demás protecciones, el patrón **no está ligado a la clase real** sino asignado al azar.
- Como **P12**, trabaja en el **espacio de features** de un modelo (latente/embedding) más que en el espacio de píxeles.

---

### P12 · *PyrFawkes: Protecting Personal Privacy Against Unauthorized Facial Recognition Models via Image Cloaking*

**Autores:** Zhen Guo, Jie Xu, Jia Xu (Hainan Univ.). **Rol:** protección de privacidad facial ("cloaking"). El abstract se describe como "this thesis".

#### P12.1 Problema investigado
Empresas o particulares pueden recolectar fotos públicas (el paper cita a Clearview AI con más de tres mil millones de imágenes) y entrenar reconocedores faciales que permiten rastrear personas sin su consentimiento (P12 §I).

#### P12.2 Propuesta
**PyrFawkes**: añadir a cada foto, antes de publicarla, un **"cloak"** (capa/manto) imperceptible que desplaza sus *features* hacia las de **otra identidad** muy distinta.

🔎 **Analogía.** Es como si en todas tus fotos públicas llevaras un maquillaje invisible para humanos pero que, para la máquina, te hace "oler" como otra persona. El sistema aprende que *tú* eres esa otra persona; cuando una cámara te ve sin maquillaje, no te reconoce.

✅ **Técnico.** Se busca δ que minimice la distancia, en el espacio de un extractor de rasgos faciales Φ, entre la foto camuflada x ⊕ δ y una foto x_T de la identidad objetivo T, sujeto a un presupuesto perceptual ρ (Eq. 1–2, 4).

#### P12.3 Funcionamiento técnico
1. **Selección de objetivo:** entre K identidades candidatas de un dataset público, se elige la que tiene el centroide de features **más lejano** del usuario: T = argmax_k min_{x∈X_U} Dist(Φ(x), C_k) (Eq. 3).
2. **Generación del cloak:** para cada foto x y una foto aleatoria x_T de T: min_δ Dist(Φ(x_T), Φ(x ⊕ δ)) + λ·max(|δ| − ρ, 0) (Eq. 4). |δ| se mide con **DSSIM** (*Structural Dissimilarity*, derivado de SSIM; refleja mejor la percepción humana que las normas Lₚ). Optimización en "espacio tanh" para mantener píxeles válidos; Adam, 1 000 iteraciones, lr 0.5.
3. **Gestión de contenido:** evitar que existan fotos sin cloak asociadas a la identidad.

#### P12.4 Datos y resultado
- **Entrada:** fotos del usuario; un extractor de features faciales; un dataset público de otras identidades.
- **Salida:** fotos camufladas para publicar.

#### P12.5 Evaluación
Extractores entrenados por los autores en **WebFace** (>500 000 imágenes, ~10 000 identidades; 90 % de identidades para entrenamiento, 10 % para test) con **DenseNet-121** e **InceptionResNet V2** (Adam, lr 0.001). El usuario protegido proviene de "PubFig" (no descrito). Métricas: *protection success rate* (tasa de error del tracker con fotos sin cloak del usuario) y *normal accuracy* (sobre otras identidades).

#### P12.6 Resultados clave e interpretación

**Tabla P12-a (P12, Tablas I y II):**

| Método | Re-identificación original ↓ | tras JPEG-75 ↓ | tras JPEG-50 ↓ | Protection rate ↑ |
|---|---|---|---|---|
| Fawkes | 6.2 % | 17.3 % | 28.4 % | 83.7 % |
| LowKey | 4.7 % | 12.5 % | 23.8 % | 78.9 % |
| **PyrFawkes** | **3.8 %** | **5.2 %** | **6.9 %** | **96.3 %** |

- **Presupuesto DSSIM** (Fig. 3, tracker Web-Incept): ρ = 0.006 → 51.6 %; 0.008 → 87.3 %; 0.01 y 0.012 → 100 %. Fig. 6: de ~72.5 % (0.002) a ~95.8 % (0.014); eligen 0.007 como compromiso.
- **Selección de objetivo** (Fig. 5): objetivo aleatorio ~82 %; similar ~70 %; disímil (propuesto) ~97 %.

**Interpretación.**
- **[Demuestra]** Con DSSIM ≤ 0.007, el protection rate es 96.3 % y la re-identificación tras JPEG es menor que en Fawkes y LowKey.
- **[Demuestra]** Elegir un objetivo disímil es importante (+15 puntos frente a aleatorio).
- **[Inferencia]** Existe una tensión entre Fig. 3 (100 % recién a partir de 0.01, con extractor de usuario menos robusto) y la elección de 0.007 con 96.3 %.

**Afirmaciones sin respaldo experimental en el md (importante):**
- **[Afirma, sin evidencia]** "Más de 80 % de protección incluso cuando imágenes limpias se filtran" (abstract): no hay experimento.
- **[Afirma, sin evidencia]** "100 % de éxito contra servicios comerciales" y, en la conclusión, "Microsoft, Amazon Rekognition y Face++": no hay experimento.
- **[Afirma, sin evidencia]** Robustez frente a detección de cloaks, adversarial training, *feature denoising* y entrenamiento desde cero: no hay experimentos.
- **[Afirma, sin evidencia completa]** La sección de disrupción menciona resize, conversión de formato y blur gaussiano, y la comparación incluye AnonymousNet y DeepPrivacy, pero la Tabla I solo muestra JPEG y tres métodos, y la Tabla II solo tres métodos.
- **[No especificado]** *Normal accuracy* nunca se reporta; número de usuarios protegidos, imágenes por usuario y repeticiones; el "segundo" dataset grande mencionado; en qué difiere algorítmicamente PyrFawkes de Fawkes (su baseline).
- **[Inferencia — problema de referencias]** Los baselines se citan como Fawkes [19], LowKey [21], AnonymousNet [23] y DeepPrivacy [25], pero en la lista de referencias del md esos números corresponden a otros trabajos (sobre deepfakes); AnonymousNet aparece como [4] y Fawkes, LowKey y DeepPrivacy no figuran en la lista. Además, la sección de trabajos relacionados trata sobre defensa contra **deepfakes**, un problema distinto.

#### P12.7 Caso aplicado
*Una periodista que investiga temas sensibles quiere seguir publicando fotos en redes sin que un sistema de reconocimiento facial la identifique en cámaras públicas.* Aplica el cloak a todas sus fotos con DSSIM ≈ 0.007. Según el paper, un reconocedor entrenado con esas fotos la confundiría en ~96 % de los casos, incluso si las fotos se recomprimen en JPEG. **Limitaciones prácticas reales:** si un amigo sube una foto suya sin cloak, la protección se debilita (el paper lo reconoce, pero no lo mide); y la eficacia contra servicios comerciales no está demostrada en el documento.

#### P12.8 Fortalezas y limitaciones
- **Fortalezas:** escenario muy concreto y relevante; selección de objetivo justificada; análisis del presupuesto perceptual (DSSIM); discusión ética (doble uso) y de adopción.
- **Limitaciones:** las señaladas arriba (afirmaciones sin experimentos, referencias inconsistentes, faltan métricas y detalles), además de depender de que el usuario camufle la mayoría de sus fotos.

#### P12.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | >95 % de protección "independientemente de cómo entrene el tracker", >80 % con fugas, 100 % contra APIs comerciales, robustez a contramedidas. |
| **Demuestra** | Tablas I–II, Figs. 3, 5 y 6 (en un setup poco descrito). |
| **Inferencia** | Gran parte de las afirmaciones del abstract no tiene soporte en el md; el resultado demostrado se limita a protection rate y re-identificación tras JPEG. |
| **No especificado** | Ver lista anterior. |

#### P12.10 Relación con los demás papers
- Es el único paper del bloque con **perturbación por imagen dirigida a una identidad objetivo** en el espacio de features (como P11 trabaja en espacio latente).
- Comparte con **P6 (EUN, CelebA-100)** el foco en **rostros**, pero P6 busca degradar todo el modelo (availability), mientras PyrFawkes busca que el modelo **confunda a una persona concreta** (más cercano a un envenenamiento dirigido).
- Su verificación empírica es la más débil del bloque, en contraste con **P5/P7/P10**, que reportan peor caso, varianza o múltiples defensas.

---

### P13 · *Reversible Unlearnable Examples: Towards the Copyright Protection in Deep Learning Era*

**Autores:** Binze Wang, Jinyu Tian, Xingrun Wang, Xiaochen Yuan, Jianqing Li (MUST Macao, FoShan Univ., Macao Polytechnic). **Publicación:** IEEE © 2025. **Rol:** protección reversible + verificación de autoría. **Código:** github (Yeah21/ReversibleUnlearnableExamples).

#### P13.1 Problema investigado
El **copyright** de un dataset enfrenta dos amenazas: (1) **entrenamiento no autorizado** (*semantic information protection*) y (2) **fuga** de datos (*ownership verification*). Combinar ingenuamente una marca de agua con unlearnable examples falla porque la perturbación **daña la extracción de la marca**: en HiDDeN el **BER** (*Bit Error Rate*, proporción de bits mal recuperados) sube de 0.39 % a 7.51 % (Fig. 1) (P13 §I).

- **Marca de agua digital (watermark)** — 🔎 *Analogía:* la firma invisible del autor en un billete. ✅ *Técnico:* embeber un mensaje binario m en la imagen con un encoder E y recuperarlo con un decoder D (HiDDeN, MBRS son métodos de referencia).

#### P13.2 Propuesta
Un marco con tres partes:
1. **Marca de agua** robusta a JPEG (basada en MBRS).
2. **Perturbación unlearnable por clase** generada **minimizando la información mutua (MI)** entre la entrada y la salida del modelo, **reversible** para usuarios autorizados.
3. **Doble extractor de marca**: un decoder para imágenes sin perturbación y otro (**D_d**) para imágenes con perturbación.

Escenario (Fig. 2): el dueño publica imágenes con marca + perturbación; los usuarios autorizados reciben por canal seguro las perturbaciones η_c y las **restan**; los no autorizados no pueden aprender; un atacante que robe las claves y redistribuya los datos limpios aún puede ser rastreado por la marca de agua.

#### P13.3 Funcionamiento técnico
- **Marca de agua (Eq. 1–2):** L₁ = MSE(x̃ − x) + log A(x̃) + BCE(m, m'), con discriminador A para calidad visual y un módulo **JPEG diferenciable** J dentro del entrenamiento para robustez a compresión.
- **Información mutua** — 🔎 *Analogía:* cuánto te ayuda saber el clima de hoy para adivinar si llevarás paraguas. Si la respuesta no depende del clima, la información mutua es cero. ✅ *Técnico:* I(X;Y) = H(Y) − H(Y|X) (Eq. 3); el objetivo es que la salida del modelo dependa lo menos posible de la semántica de la imagen.
- **Derivación (Eq. 4–6):** min I(X̂; Ŷ) con ‖η‖∞ < ε; argumentan que en un modelo determinista H(Ŷ|X̂) = 0, por lo que minimizan H(Ŷ); en la práctica la pérdida implementada es E[−f(J(x̃+η)) log f(J(x̃+η))], es decir, la **entropía de la predicción de cada muestra** (con JPEG diferenciable). **[Inferencia]** Esa pérdida es la entropía *condicional por muestra* (minimizarla vuelve al modelo muy "seguro" de sus predicciones), no la entropía marginal H(Ŷ) que aparece en la derivación; el paper no discute esta diferencia. Conceptualmente queda cerca de las perturbaciones *error-minimizing*, pero sin usar etiquetas en el objetivo.
- **Algoritmo 1:** alternar (a) T épocas entrenando la víctima f con CE sobre datos perturbados y (b) un paso de gradiente para cada ηᵢ (Eq. 7) recortado a ε; luego **promediar por clase**: η_c = media de ηᵢ de la clase c (Eq. 8). Repetir hasta un criterio de exactitud e.
- **Doble extracción (Eq. 9):** L₂ = BCE(m, D_d(J(x̃ + η))). La autoría se verifica si **cualquiera** de los dos decoders recupera m.
- **Reversibilidad:** x̂ = x̃ + η_c; el usuario autorizado calcula x̃ = x̂ − η_c (exacto, porque η_c es la misma para toda la clase).

#### P13.4 Datos y resultado
- **Entrada:** dataset etiquetado; mensaje de marca de agua (64 bits en ImageNet-100 a 256×256; 32 bits en CIFAR-10); ε (por defecto 4/255; dicen que 8/255 ya no garantiza imperceptibilidad, Fig. 7).
- **Salida:** imágenes protegidas x̂; un conjunto de C perturbaciones η_c (la "clave"); encoder E, decoders D y D_d.

#### P13.5 Evaluación
ImageNet-100, CIFAR-10, Oxford-IIIT Pets, ImageNet-10 (en EasyDL); víctima de generación ResNet18; evaluación en ResNet18, VGG19, MobileNetV2, GoogLeNet, DenseNet121; baselines GEAA y Adv-watermark (marco completo), y Emax, Emin, Tattack, Linear, Conv, Autoregressive, Gradient alignment (solo protección); 6 repeticiones con media ± desviación en la Tabla VI. Validación en la plataforma comercial **Baidu EasyDL** ("fastest training"). PSNR y SSIM para calidad.

#### P13.6 Resultados clave e interpretación

**Tabla P13-a (P13, Tabla II; exactitud promedio de 5 modelos: protegida (tras revertir); BER con / sin perturbación (PSNR)):**

| Dataset | Método | Exactitud protegida (recuperada) | BER con perturb. | BER sin perturb. |
|---|---|---|---|---|
| ImageNet-100 | Original | 72.78 | — | — |
| ImageNet-100 | GEAA | 42.35 (66.66) | — (PSNR 9.23) | 26.46 % (34.77) |
| ImageNet-100 | Adv-watermark | 58.18 (—) | — (PSNR 28.44) | — |
| ImageNet-100 | **Ours ε=4** | **6.76 (72.23)** | **0.16 % (41.25)** | **0.22 % (48.36)** |
| ImageNet-100 | Ours ε=8 | 3.77 (71.92) | 0.27 % (35.54) | 0.26 % (48.35) |
| ImageNet-100 | Ours ε=16 | 1.60 (71.34) | 0.32 % (30.74) | 0.35 % (48.21) |
| Pets | Original | 70.94 | — | — |
| Pets | Ours ε=8 | 2.70 en los 5 modelos (68.56) | 0.25 % (37.07) | 0.08 % (48.80) |
| CIFAR-10 | Original | 90.29 | — | — |
| CIFAR-10 | GEAA | 45.55 (70.34) | — | 28.86 % (36.28) |
| CIFAR-10 | Ours ε=4 | 11.79 (89.96) | 2.03 % (34.69) | 1.29 % (38.27) |

**Tabla P13-b — Robustez (P13, Tablas III, VII, VIII):**

| Escenario | Emin | Emax | Ours |
|---|---|---|---|
| EasyDL CIFAR10 / ImageNet10 / Pets (limpio 92.20 / 77.00 / 67.03) | 19.47 / 23.80 / 37.24 | 18.88 / 20.60 / 20.58 | **16.33 / 13.80 / 18.14** |
| JPEG QF75, promedio 5 modelos CIFAR-10 (limpio 88.34) | 49.34 | 52.91 | 63.53 sin JPEG diferenciable · **39.12 con JPEG diferenciable** |
| AT ρ=2, promedio | 57.15 | — | **15.09** |
| AT ρ=4, promedio | 52.15 | — | **45.84** |
| 10 % limpio + 90 % protegido | 77.18 | — | **66.84** |

**Otros:** CIFAR-10 sin defensa, promedio 5 modelos: Ours 11.45 % vs Emax 17.62, Emin 17.82, Autoregressive 23.56, Gradient alignment 68.52 (Tabla VI); augmentations (Gaussian noise, CutMix, Cutout, Mixup) 10.37–12.88 % (Tabla V); reducción de MI frente a modelos limpios: 97 % (ResNet18, DenseNet121), 82–89 % (otros); ablación del extractor (Tabla IV, ε=8): single BER 0.98/0.50 %, dual 0.38/0.19 %, dual_f (encoder congelado) 0.51/0.33 % con mejor PSNR (48.21 vs 45.82).

**Interpretación.**
- **[Demuestra]** Se logran ambos objetivos a la vez en ImageNet-100: exactitud no autorizada ~7 % (ε=4), autorizada ~72 % (≈99 % de la original), y marca de agua recuperable con BER < 0.4 % tras JPEG-75.
- **[Demuestra]** El doble extractor reduce el daño de la perturbación sobre la marca (Tabla IV).
- **[Demuestra]** La compresión JPEG **debilita** la protección (sin módulo diferenciable, 63.53 %; con él, 39.12 %), y **AT con ρ = 4** recupera bastante (45.84 % promedio; ResNet18 39.79 %, MobileNetV2 68.09 %, GoogLeNet 69.22 %).
- **[Inferencia]** En Pets, los cinco modelos (arquitecturas muy distintas) dan exactamente 2.70 %. Un valor idéntico en todas sugiere que los modelos colapsan a una predicción constante (todas las imágenes a la misma clase), lo que sería coherente con una protección muy fuerte; el md no comenta este patrón ni indica el número de clases de Pets, por lo que no puede verificarse aquí.
- **[Inferencia]** En la Tabla VIII, VGG19 obtiene 10.00 % con ambos métodos y ambos ρ: podría indicar que el entrenamiento adversarial de VGG19 no convergió; no se comenta.
- **[Inferencia]** En la ablación, el texto dice que dual_f "degrada el BER de 0.81 % a 1.33 %", pero en la tabla dual_f (0.81 %) es **mejor** que dual (1.33 %) con ε=16: inconsistencia de redacción.
- **[Inferencia — riesgo de seguridad]** La reversibilidad depende de mantener secretas las η_c, pero son **aditivas y por clase**. Según P5, los venenos aditivos por clase son linealmente separables y detectables; según P10, las perturbaciones aditivas por clase pueden estimarse con la imagen promedio de la clase. Ninguna de estas amenazas se evalúa en P13.

#### P13.7 Caso aplicado
*Una editorial con un archivo de 100 000 ilustraciones quiere (a) venderlo a clientes que entrenarán modelos y (b) impedir que piratas lo usen.* Publica las ilustraciones protegidas; a cada cliente le entrega por canal seguro las 100 perturbaciones de clase (la "clave"); el cliente las resta y entrena normalmente (~72 % vs ~73 % original). Un pirata que descarga las imágenes publicadas obtiene un modelo inútil (~7 %). Si un cliente filtra el dataset ya "limpio", la editorial puede probar que es suyo extrayendo la marca de agua (BER ~0.2 %). **Riesgos:** si el pirata comprime en JPEG o usa AT con presupuesto 4/255, recupera bastante; y si estima las perturbaciones de clase, la clave pierde valor.

#### P13.8 Fortalezas y limitaciones
- **Fortalezas:** primer marco (según los autores) que une protección reversible y verificación de autoría; doble extractor bien justificado por la Fig. 1 y la Tabla IV; validación en plataforma comercial; repeticiones con desviación estándar; análisis de JPEG, augmentations, AT y mezcla de datos limpios.
- **Limitaciones inferidas:** derivación de MI con un paso discutible; seguridad de la clave no analizada; protección débil frente a AT ρ=4 y JPEG; criterio de parada e no especificado numéricamente; costo de generación no reportado; el baseline Adv-watermark requirió 10 días de cómputo según los autores.

#### P13.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Comprehensive copyright protection"; la perturbación MI es altamente generalizable. |
| **Demuestra** | Tablas II–IX; reducción de MI; CAM (Fig. 4, cualitativa). |
| **Inferencia** | Posible colapso a predicción constante en Pets; riesgo de estimación de la clave; tensión en la derivación de MI. |
| **No especificado** | Valor de e; tiempo de generación; robustez a detección o estimación de η_c. |

#### P13.10 Relación con los demás papers
- Es el único paper del bloque que trata la **reversibilidad** y la **autoría**; los demás asumen que el protector nunca necesita "deshacer" la protección (CUDA incluso pide **borrar** las originales, P2).
- Su perturbación aditiva por clase es exactamente el tipo de veneno que **P5** demuestra linealmente separable y que **P10** considera recuperable por promedios.
- Comparte con **P9 (KBS)** la idea de esconder información en la imagen (código binario / marca de agua) y con **P3** la preocupación por "marcas" textuales o visuales que pueden eliminarse.

---

## 4. Análisis comparativo entre los 13 papers

### 4.1 Taxonomía

```
                         ┌─────────────────────────────────────────────────────────┐
                         │ DATOS PUBLICADOS POR EL DUEÑO                           │
                         └─────────────────────────────────────────────────────────┘
   PROTECCIÓN (hacer no aprendible)                    ATAQUE / DEFENSA (volver aprendible)
   ─────────────────────────────────                    ─────────────────────────────────────
   Aditiva, con presupuesto Lp                          Cambiar el ENTRENAMIENTO
     · GUE (P8)  sample-wise, generador                   · Ensembles + transf. no lineales (P1)
     · Reversible MI (P13)  class-wise + watermark        · SDA+AN (P5)
     · PyrFawkes (P12)  por imagen, DSSIM                 · VESPR: SSL+SL+AT guiado por SL (P7)
     · Robust features clustering (P11)  universal      Cambiar los DATOS
   Multiplicativa / sin presupuesto Lp                    · COIN: interpolación aleatoria (P4)
     · CUDA (P2)   filtro aleatorio por clase             · CMCR: difusión + texto (P3)
     · VUDA/HUDA (P4)  filtros vertical/horizontal      DETECTAR
     · EUN (P6)    CUDA + 1 píxel                         · EPD: bordes + SVM (P4) — por imagen
     · IRP (P10)   CUDA + restauración imperfecta         · Simple Networks / Bias-shift (P5) — por dataset
     · KBS (P9)    código oculto en Fourier
```

### 4.2 Tabla comparativa de las propuestas

| Paper | Tipo | ¿Necesita modelo sustituto? | Granularidad | Restricción de visibilidad | Dominio | Datasets principales | Defensas/ataques contra los que se evalúa |
|---|---|---|---|---|---|---|---|
| P1 | Ataque | — (usa VGG/ResNet preentrenadas) | — | — | Imagen (clasificación) | CIFAR-10 (+MNIST, ImageNet 2 clases, CIFAR-100) | 12 protecciones |
| P2 CUDA | Protección | No | Por clase | p_b (blur), sin Lₚ | Imagen | CIFAR-10/100, ImageNet-100 | AT (L∞, L2), augmentations, smoothing, fine-tuning, DAT |
| P3 CMCR | Ataque | Difusión + BART preentrenados | — | — | Imagen + texto | Flickr8K/30K, MS-COCO | MEM |
| P4 EPD/COIN | Detección + defensa (+2 protecciones) | No (SVM simple) | Por imagen | — | Imagen | CIFAR-10/100, ImageNet20/100 | CUDA, VUDA, HUDA (+URP, OPS) |
| P5 | Detección + defensa + teoría | Red de 2 capas | Por dataset | — | Imagen | CIFAR-10/100, TinyImageNet | ~15 venenos |
| P6 EUN | Protección | No (búsqueda del píxel) | Por clase | p_b; 1 píxel | Imagen | CIFAR-10/100, ImageNet-100, CelebA-100 | AT, ISS, UEraser |
| P7 VESPR | Defensa | — | — | — | Imagen | ImageNet-100, CIFAR-10 | 7 venenos |
| P8 GUE | Protección | Sí (clasificador en el juego) + generador U-Net | Por muestra | L∞ 8/255 | Imagen | CIFAR-10/100 | Mixup, Cutout, CutMix, AT |
| P9 KBS | Protección | No (autoencoders + extractores) | Por clase (código) | Pérdida de reconstrucción (sin ε) | Fourier → imagen | CIFAR-10/100, ImageNet-100 | AT, augmentations, RSKFF |
| P10 IRP | Protección | No (regresión lineal por clase) | Por clase | Sin Lₚ; métricas de calidad | Imagen | CIFAR-10/100, STL-10, ImageNet-100 | SL, SSL, AT, ISS, augmentations, AVATAR, UEraser, COIN |
| P11 | Ataque/protección | Sí (white-box clustering) | Universal por cluster, asignado al azar | L∞ 0.05–0.3 | Imagen / voz / tabular | Iris, MNIST, F-MNIST, TIMIT, CIFAR-2 | Defensa robusta [17] |
| P12 PyrFawkes | Protección | Sí (extractor facial) | Por imagen | DSSIM ≤ 0.007 | Rostros | WebFace (+PubFig) | JPEG |
| P13 Reversible | Protección + watermark | Sí (ResNet18 víctima) | Por clase | L∞ 4/255 | Imagen | ImageNet-100, CIFAR-10, Pets | Augmentations, JPEG, AT, EasyDL |

### 4.3 Similitudes clave

1. **El atajo (shortcut) como mecanismo común.** CUDA (filtro↔etiqueta, P2), EUN (filtro + píxel, P6), IRP (sesgo por desplazamiento de picos, P10), KBS (código binario recuperable, P9), P13 (perturbación que domina la atención, CAM en Fig. 4), P11 (robust features dominantes) y PyrFawkes (features de otra identidad) comparten la misma lógica: **ofrecer a la red algo más fácil de aprender que la semántica**.
2. **Por clase como estrategia dominante.** De las protecciones propuestas en el bloque (CUDA, VUDA/HUDA, EUN, GUE, KBS, IRP, robust features de P11, PyrFawkes y la de P13), siete son por clase o universales; solo GUE y PyrFawkes generan una perturbación distinta por muestra/imagen.
3. **AT como prueba de fuego.** Casi todos los papers usan AT L∞ 4/255 como referencia (P2, P6, P8, P9, P10, P13), y los papers de defensa lo usan como baseline principal (P1, P4, P5, P7).
4. **ResNet-18 como modelo de referencia** para generar y evaluar (P2, P5, P6, P7, P8, P9, P10, P13).
5. **Preocupación por el costo**: P2 (segundos), P5 (Tabla 12–13), P9 (Tabla 8), P11 (Tabla 6), P3 (61 h), P1 (Tabla 7).

### 4.4 Diferencias clave

| Eje | Contraste |
|---|---|
| **Objetivo de aprendizaje atacado** | Clasificación supervisada (casi todos) vs **SSL** (P7, P10) vs **multimodal contrastivo** (P3) vs **clustering no supervisado** (P11) vs **reconocimiento facial** (P12). |
| **Dónde se inserta la protección** | Espacio de píxeles aditivo (P8, P13), convolución espacial (P2, P4-VUDA/HUDA, P6, P10), dominio de Fourier (P9), espacio de features (P11, P12). |
| **Reversibilidad** | Solo P13 la contempla; CUDA recomienda borrar las imágenes limpias (P2). |
| **Fundamento teórico** | Teoremas con prueba: P2 (GMM), P5 (separabilidad y presupuestos), P8 (existencia de equilibrio), P10 (picos y restauración imperfecta), P4 (hipótesis validada empíricamente en GMM). Sin teoría formal: P1, P3, P6, P7, P9, P11, P12, P13 (P13 tiene una derivación de MI, no un teorema). |
| **Nivel de evidencia** (valoración propia, **[Inferencia]**) | Muy alto (múltiples defensas, peor caso o varianza): P5, P7, P10. Alto: P1, P2, P4, P9, P13. Medio: P6, P8, P11, P3. Bajo: P12. |
| **Métrica de éxito** | Exactitud limpia (casi todos); Recovery Rate y Medr (P3); ACC/NMI no supervisada (P11); protection rate (P12); BER/PSNR para la marca (P13); ACC/AUC de detección (P4, P5). |

### 4.5 "Duelos" documentados entre papers (con cifras de los propios papers)

La tabla resume los únicos cruces donde **un paper evalúa directamente el método de otro** de este bloque. No se combinan cifras de setups distintos salvo que se indique.

| Protección | Defensa / ataque | Resultado | Fuente |
|---|---|---|---|
| CUDA (P2) | COIN (P4) | 24.92 % → 72.41 % (CIFAR-10, prom. 4 modelos); 22.60 % → 37.48 % (ImageNet100) | P4 Tablas 2–3 |
| CUDA (P2) | COIN (P4) | 71.90 % (CIFAR-10, ResNet-18) | P10 Tabla 8 |
| IRP (P10) | COIN (P4) | 53.32 % (CIFAR-10, ResNet-18) | P10 Tabla 8 |
| CUDA (P2) | Bias-shifting test (P5) | No detectado (13.62 % / 25.08 % < 70 %) | P5 Tabla 16 |
| CUDA (P2) | Simple Networks, 2-NN (P5) | Detectado (80.40 %) | P5 Tabla 16 |
| CUDA (P2) | VESPR (P7) | 6.10 % (SL) → 45.20 % (ImageNet-100) | P7 Tabla 2 |
| CUDA (P2) | RSKFF (citado en P9) | 18.48 % → 78.53 % (CIFAR-10) | P9 Tabla 5 |
| KBS (P9) | RSKFF | 13.53 % → 42.53 % (CIFAR-10) | P9 Tabla 5 |
| GUE (P8) | AT 4/255 (en KBS) | 52.79 % (CIFAR-10) | P9 Tabla 1 (coincide con P8 Tabla 4, GUE-AT(8-4)) |
| CUDA (P2) | ISS Gray+JPEG (P6, P10) | 23.19 % (P6) / 21.94 % (P10) | P6 Tabla 7; P10 Tabla 1 |
| CUDA (P2) | SSL (P10) | 66.58 % (CIFAR-10) | P10 Tabla 1 |
| 12 protecciones (no incluye CUDA) | Ensembles (P1) | ≥ 89 % | P1 Tablas 1, 2, 4 |

**[Inferencia]** Lecturas cruzadas útiles:
- **CUDA es la protección más atacada y la más "derrotada"** dentro del bloque (COIN, RSKFF, VESPR, 2-NN de P5).
- **Ninguna de las nuevas protecciones convolucionales (EUN, KBS) se evaluó contra COIN o EPD**; solo IRP lo hizo, y aun así COIN le sube a 53.32 %.
- **P1 no incluyó CUDA ni sus sucesores** entre sus 12 protecciones, de modo que no sabemos si sus ensembles con transformaciones no lineales romperían CUDA, EUN, IRP o KBS. Sabemos, por P2 y P9, que grayscale **no** rompe CUDA (20.12 %) y rompe parcialmente KBS (35.24 %).

### 4.6 Comparación del "peor caso" reportado para cada protección (CIFAR-10, ResNet-18, cuando existe)

| Protección | Peor caso documentado en este bloque | Escenario | Fuente |
|---|---|---|---|
| CUDA | 78.53 % | RSKFF | P9 Tabla 5 |
| CUDA | 71.90 % | COIN | P10 Tabla 8 / P4 Tabla 2 (RN18) |
| EUN | 35.07 % | UEraser-Max | P6 Tabla 8 |
| GUE | 76.96 % | AT 4/255 | P8 Tabla 3 |
| GUE-AT(8-4) | 52.79 % | AT 4/255 | P8 Tabla 4 |
| KBS | 42.53 % | RSKFF | P9 Tabla 5 |
| IRP | 54.78 % | AVATAR | P10 Tabla 8 |
| Reversible (P13) | 63.53 % (sin JPEG dif.) / 45.84 % prom. (AT ρ=4) | JPEG QF75 / AT | P13 Tablas VII–VIII |

**[Inferencia]** Estas cifras **no son directamente comparables** (cada paper eligió distintas defensas). Lo que sí muestran es que **cada protección tiene una defensa que la lleva por encima del 35–79 %**, frente a ~90–95 % del entrenamiento limpio. La conclusión prudente es que las protecciones actuales *reducen* la utilidad del dato robado, pero no la *anulan* frente a un adversario informado.

### 4.7 Coincidencias y discrepancias numéricas entre papers

- **Cifras idénticas reutilizadas.** EM 13.20, TAP 22.51, NTGA 16.27, REM 27.09, CUDA 18.48 (CIFAR-10 ERM) y sus contrapartes bajo AT aparecen en P2, P6 y P9. P2 declara que los toma de Fu et al.; P6 y P9 no lo declaran. **[Inferencia]** No son tres experimentos independientes.
- **Mismos números entre P7 y P10** (mismo grupo): ImageNet-100 Clean 77.66 / SL+AT 69.76 / CUDA SL 6.10 / CUDA SSL 26.12, etc. CP en SSL es 14.80 % en P7 y 18.76 % en P10 — **discrepancia no explicada**.
- **COIN sobre CUDA**: 71.90 % en P4 (ResNet18, CIFAR-10) y 71.90 % en P10 → **consistente** (P10 probablemente reutiliza el número de P4).
- **Exactitud limpia de ResNet-18 en CIFAR-10**: 94.66 % (P2, P6, P9), 93.86 % (P7, P10), 92.36 % (P8), 94.49 % (P13): **setups de entrenamiento distintos** → comparar exactitudes absolutas entre papers exige cautela.
- **CUDA sin defensa en CIFAR-10**: 18.48 % (P2), 21.89 % (P10), 26.49 % (P4, ResNet18): otra muestra de sensibilidad al setup (épocas, augmentations).

### 4.8 Mapa de relaciones

```
P2 CUDA ──► P6 EUN (CUDA+OPS)
   │    ──► P10 IRP (CUDA restaurado) ──evaluado contra──► P4 COIN
   │    ──► P9 KBS (crítica de CUDA; usa RSKFF)
   │    ◄── P4 EPD/COIN (detecta y corrompe; crea VUDA/HUDA)
   │    ◄── P5 (2-NN lo detecta; bias-shift no)
   │    ◄── P7 VESPR (lo neutraliza parcialmente en ImageNet-100)
P5 (Gao) ── mismo grupo ── P8 GUE (Gao) ──baseline de──► P9 KBS
P7 VESPR (Kong) ── mismo grupo ── P10 IRP (Kong)
P1 Ensembles ──ataca──► 12 protecciones aditivas/sintéticas (EM, REM, OPS, AR, ...)
P3 CMCR ──ataca──► MEM (multimodal)
P11 clustering, P12 rostros, P13 copyright ── dominios propios, sin cruces directos
```

---

## 5. Casos aplicados transversales

Los siguientes escenarios integran varios papers para mostrar **qué significa cada resultado en la práctica**. Las cifras se toman de los papers y se señalan como **[Inferencia]** las extrapolaciones.

### Caso A — Persona que publica fotos personales en redes sociales
- **Amenaza:** un tercero entrena un reconocedor facial con sus fotos.
- **Opciones del bloque:** PyrFawkes (P12) apunta exactamente a este caso (protection rate 96.3 %); EUN (P6) muestra 2–10 % de exactitud en CelebA-100 con varias arquitecturas.
- **Riesgos:** fotos sin proteger subidas por otros (P12 lo admite; P2, P9 y P10 muestran que la protección parcial casi no funciona); el adversario puede usar AT o purificación (P5, P7, P10). **[Inferencia]** Para una persona, la protección es útil solo si controla casi todas sus imágenes públicas.

### Caso B — Artista o ilustrador que publica su portafolio
- **Amenaza:** entrenamiento supervisado *o* auto-supervisado sobre su estilo.
- **Opción más sólida del bloque:** IRP (P10), por su peor caso bajo (43.24 % CIFAR-10, 22.10 % ImageNet-100) y buena calidad visual (SSIM 0.775).
- **Riesgos:** AVATAR (difusión) lleva a 54.78 %; COIN a 53.32 % (P10 Tabla 8). **[Inferencia]** La artista debería asumir que un adversario con purificación recupera más de la mitad de la utilidad.

### Caso C — Empresa que vende un dataset y quiere controlar quién lo usa
- **Opción del bloque:** Reversible Unlearnable Examples (P13): exactitud no autorizada ~7 %, autorizada ~72 %, marca de agua verificable (BER ~0.2 %).
- **Riesgos:** JPEG (63.53 % sin módulo diferenciable), AT ρ=4 (45.84 %), filtración de la clave; **[Inferencia]** posible estimación de la clave por promedios de clase (amenaza mencionada en P10 para perturbaciones aditivas por clase).

### Caso D — Equipo de ML que compra o descarga datos de terceros (posiblemente envenenados)
- **Paso 1, detección barata:** P5 (lineal / 2-NN / bias-shift) a nivel de dataset; P4-EPD por imagen para protecciones convolucionales. Combinar ambos, porque el bias-shift no detecta CUDA y el lineal no detecta AR ni CP (P5 Tabla 16).
- **Paso 2, entrenar a pesar del veneno:** si es convolucional, COIN (P4); si es aditivo tipo Region/Err-min/NTGA/TUE, SDA+AN (P5) es barato; si no se sabe nada, VESPR (P7) maximiza el peor caso; si se busca máxima simplicidad, transformaciones no lineales + ensembles (P1).
- **Lección ética:** estos mismos pasos permiten ignorar la voluntad del dueño de los datos; los papers P1, P3 y P5 lo presentan como evaluación de robustez ("red-team"), no como recomendación de uso no autorizado.

### Caso E — Dataset multimodal (imágenes con descripciones)
- **Amenaza para el dueño:** CMCR (P3) recupera ~89 % del rendimiento de recuperación protegido por MEM, sobre todo quitando el trigger textual de posición fija.
- **Lección:** proteger cada modalidad por separado no basta; P3 recomienda triggers dinámicos y consistencia cruzada.

### Caso F — Datos compartidos para segmentación o clustering de clientes
- **Opción del bloque:** robust features de P11 (VaDE en MNIST 92.38 % → 11.25 %).
- **Riesgos:** requiere acceso white-box y perturbaciones grandes; no es reversible, así que el servicio legítimo tampoco funcionaría.

---

## 6. Vacíos, inconsistencias detectadas y límites de este informe

### 6.1 Vacíos comunes de la literatura (según estos 13 papers)
1. **Protección parcial:** todas las protecciones pierden efecto si no cubren casi el 100 % de los datos (P2, P8, P9, P10); P5 añade que la detección también requiere ≥ 60 % envenenado.
2. **Ataques adaptativos:** pocas protecciones se prueban contra defensas diseñadas específicamente contra ellas (excepciones: CUDA con DAT en P2, IRP con COIN en P10).
3. **Alta resolución:** muchos resultados se concentran en CIFAR (32×32); en ImageNet-100 las defensas pierden ventaja (COIN en P4) y las brechas con el limpio son mayores (P7).
4. **Calidad visual cuantificada:** solo P10 (LPIPS, SSIM, MS-SSIM, CLIP-IQA, BRISQUE), P13 (PSNR, SSIM) y P12 (DSSIM como presupuesto) la reportan; CUDA, EUN y KBS no.
5. **Reproducibilidad:** pocos reportan varianza (P1, P13 y parcialmente P2); varios remiten a material suplementario no incluido en los md (P1, P9).

### 6.2 Inconsistencias o puntos débiles detectados en los md (listado)
| Paper | Observación |
|---|---|
| P1 | Uso del test set para elegir hiperparámetros y para detener el stacking (α); valor de DC en Tabla 5 (90.30) no coincide con el máximo de sus tablas (90.17). |
| P3 | Eq. 9 condiciona en "Medr ≥ τ_thresh" pero el umbral se define sobre recall/Recovery Rate; Tabla III (COCO, RN50: 102/115) no coincide con la Tabla I (358/293); significado de MEM-3/MEM-5 no especificado. |
| P4 | La Tabla 4 rotula "CUDA CIFAR-10" una columna que el texto atribuye a VUDA; Eq. 9 usa s_x en ω_y (errata). |
| P6 | Cifras de baselines aparentemente reutilizadas sin declararlo. |
| P8 | La garantía de "ataque más poderoso" es para L_acc, no para L_sur; GUE-AT pierde frente a REM con ε_d = 4/255 sin comentario. |
| P9 | Texto vs tabla: "consistently lower" contradicho por L∞ 16/255 y por WRN-34-10; 16.32 % atribuido a 16/255 cuando corresponde a CIFAR-100 con 8/255; texto de la ablación de extractores invierte "magnitude-only" y "phase-only" respecto a las filas de la tabla. |
| P10 | "Outperforms … in all cases" contradicho por ISS (CUDA mejor) y por dos arquitecturas en CIFAR-100. |
| P11 | Tabla 5 (con defensa) idéntica a Tabla 2 (sin defensa) en datos envenenados; Tabla 8 "1&2" vs texto "3&2". |
| P12 | Varias afirmaciones del abstract y la conclusión sin experimentos; referencias de baselines no coinciden con la bibliografía; *normal accuracy* nunca reportada. |
| P13 | Paso de la derivación de MI (H(Ŷ) vs entropía por muestra); redacción de la ablación dual/dual_f contradice la tabla; VGG19 = 10.00 % en todas las filas de AT. |

### 6.3 Límites de este informe
- Se basa en conversiones a Markdown de los PDFs; algunas figuras solo están como descripciones o como valores aproximados extraídos automáticamente (marcados con "~"). Donde cito "~", el valor es aproximado.
- No se consultó material suplementario externo ni código; cuando un dato depende de ellos se marca **[No especificado]**.
- Las comparaciones entre papers se hacen solo cuando un paper evalúa explícitamente a otro, o se marcan como **[Inferencia]** cuando cruzo cifras de setups diferentes.

---

## Anexo A. Archivos fuente

| ID | Archivo Markdown en el repositorio |
|---|---|
| P1 | `Advancing ensemble learning against unlearnable data/Advancing ensemble learning against unlearnable data.md` |
| P2 | `CUDA Convolution-Based Unlearnable Datasets/CUDA Convolution-Based Unlearnable Datasets.md` |
| P3 | `Cross-Modal-Collaborative-Recovery-Breaking-Multimodal-Unlearnable-Examples-Protection/Cross-Modal-Collaborative-Recovery-Breaking-Multimodal-Unlearnable-Examples-Protection.md` |
| P4 | `Detecting and Corrupting Convolution-based Unlearnable Examples/Detecting and Corrupting Convolution-based Unlearnable Examples.md` |
| P5 | `Detection and Defense of Unlearnable Examples/Detection and Defense of Unlearnable Examples.md` |
| P6 | `EUN Enhanced unlearnable examples generation approach for privacy/EUN Enhanced unlearnable examples generation approach for privacy.md` |
| P7 | `Exploiting Supervised Poison Vulnerability to Strengthen Self-supervised Defense/Exploiting Supervised Poison Vulnerability to Strengthen Self-supervised Defense.md` |
| P8 | `Game-Theoretic Unlearnable Example Generator/Game-Theoretic Unlearnable Example Generator.md` |
| P9 | `K-Space Bispectrum Steganography for Robust Unlearnable Data/K-Space Bispectrum Steganography for Robust Unlearnable Data.md` |
| P10 | `Leveraging Imperfect Restoration for Data Availability Attack/Leveraging Imperfect Restoration for Data Availability Attack.md` |
| P11 | `Novel poisoning attacks for clustering methods via robust feature generation/Novel poisoning attacks for clustering methods via robust feature generation.md` |
| P12 | `Protecting-Personal-Privacy-Against-Unauthorized-Facial-Recognition-Models-via-Image-Cloaking/Protecting-Personal-Privacy-Against-Unauthorized-Facial-Recognition-Models-via-Image-Cloaking.md` |
| P13 | `Reversible_Unlearnable_Examples__Towards_the/Reversible_Unlearnable_Examples__Towards_the.md` |
