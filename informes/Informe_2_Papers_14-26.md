# Informe 2 — Unlearnable Examples: nuevas protecciones, nuevos dominios, detección y ataques. Análisis exhaustivo de los papers 14–26

> **Alcance.** Este informe trabaja **exclusivamente** con los 13 artículos restantes del repositorio (carpetas 14 a 27 en orden alfabético). No incorpora información externa: cada dato numérico, fórmula o afirmación proviene del archivo Markdown del paper correspondiente. Cuando un paper reporta una cifra de otro trabajo (por ejemplo, de un baseline), se indica que la cifra la reporta *ese* paper.
>
> **Sobre el número de carpetas.** El repositorio tiene 27 carpetas porque *ARMOR: Shielding Unlearnable Examples against Data Augmentation* aparece **dos veces** (`armor Shielding Unlearnable Examples against` y `armor_Shielding_Unlearnable_Examples_against`). Son dos conversiones del mismo artículo; se trata como **un solo paper (P25)** y se usó como fuente principal la segunda carpeta (conversión más limpia), verificando las cifras con la primera cuando fue necesario. Así, este bloque contiene exactamente 13 papers (P14–P26).
>
> **Relación con el Informe 1.** Los papers P1–P13 se analizaron en `Informe_1_Papers_01-13.md`. En este informe se hace referencia a ellos como "Informe 1, Px" solo para **comparar**, nunca para añadir datos que no estén en los papers 14–26.

---

## Índice

- [0. Cómo leer este informe](#0-cómo-leer-este-informe)
- [1. Conceptos fundamentales (glosario explicado)](#1-conceptos-fundamentales-glosario-explicado)
- [2. Resumen completo y exhaustivo de los 13 papers](#2-resumen-completo-y-exhaustivo-de-los-13-papers)
- [3. Informe detallado por paper](#3-informe-detallado-por-paper)
  - [P14 · TPD (defensa contra perturbaciones + ruido de etiquetas)](#p14--tpd-two-pronged-defense)
  - [P15 · Segue (UE generativo para rostros)](#p15--segue)
  - [P16 · SEM (UE estable frente a adversarial training)](#p16--sem-stable-error-minimizing)
  - [P17 · T2UE (UE generado solo desde texto)](#p17--t2ue-text-to-unlearnable)
  - [P18 · Temporal UE (video / object tracking)](#p18--tue-temporal-video-tracking)
  - [P19 · Optimización del Bayes error](#p19--bayes-error-optimization)
  - [P20 · Unlearnable Clusters (UC / UC-CLIP)](#p20--unlearnable-clusters-uc--uc-clip)
  - [P21 · Iterative Filtering (detección de UEs)](#p21--iterative-filtering-if)
  - [P22 · Learnable Examples (LE / JCDP)](#p22--learnable-examples-le--jcdp)
  - [P23 · Detección de cloaking facial (ELA + img2vec + SVM)](#p23--detección-de-cloaking-ela--img2vec--svm)
  - [P24 · WDS (Wavelet Domain Steganography)](#p24--wds-wavelet-domain-steganography)
  - [P25 · ARMOR (UE resistente a data augmentation)](#p25--armor)
  - [P26 · Dominant image features (clean-label poisoning)](#p26--dominant-image-features-clean-label)
- [4. Análisis comparativo entre los 13 papers (y con el Informe 1)](#4-análisis-comparativo-entre-los-13-papers-y-con-el-informe-1)
- [5. Casos aplicados transversales](#5-casos-aplicados-transversales)
- [6. Vacíos, inconsistencias detectadas y límites de este informe](#6-vacíos-inconsistencias-detectadas-y-límites-de-este-informe)
- [Anexo A. Archivos fuente](#anexo-a-archivos-fuente)

---

## 0. Cómo leer este informe

### 0.1 Los 13 papers y su identificador

| ID | Título (abreviado) | Rol principal | Lugar de publicación visible en el md |
|---|---|---|---|
| **P14** | *Robust Learning with Adversarial Perturbations and Label Noise: A Two-Pronged Defense Approach* (TPD; Zhang, Huang, Luo, Zhao) | **Defensa** (entrenar bien con datos envenenados *y* etiquetas ruidosas) | ACM MMAsia '22 (DOI 10.1145/3551626.3564934) |
| **P15** | *Segue: Side-information Guided Generative Unlearnable Examples for Facial Privacy Protection* (Zhang, Zhang, Zhang, Zhou, Zhang, Yu) | **Protección** generativa de rostros | No visible en el md |
| **P16** | *Stable Unlearnable Example (SEM)* (Liu, Xu, Chen, Sun) | **Protección** robusta a adversarial training | AAAI 2024 (línea de copyright) |
| **P17** | *T2UE: Generating Unlearnable Examples from Text Descriptions* (Ma, Huang, Song, Sun, Gao, Jiang) | **Protección** multimodal "zero-contact" | No visible en el md |
| **P18** | *Temporal Unlearnable Examples: Preventing Personal Video Data from Unauthorized Exploitation by Object Tracking* (Wu, Yu, Kong, Liu, Wan, Li, Kot, Chan) | **Protección** de video (tracking) | No visible (formato CVF) |
| **P19** | *Towards Provably Unlearnable Examples via Bayes Error Optimization* (Zhang, Sun, Lim, Zhang) | **Protección** con garantía teórica | AAAI 2026 (línea de copyright) |
| **P20** | *Unlearnable Clusters: Towards Label-agnostic Unlearnable Examples* (Zhang, Ma, Yi, Sang, Jiang, Wang, Xu) | **Protección** sin depender de etiquetas | No visible en el md; P15 lo cita como CVPR 2023 |
| **P21** | *Unlearnable Examples Detection via Iterative Filtering* (Yu, Zheng, Yang, Yang, Liu, Lu, Tan, Lam, Kot) | **Detección** de UEs en un dataset mixto | No visible (formato LNCS) |
| **P22** | *Unlearnable Examples Give a False Sense of Security: Piercing through Unexploitable Data with Learnable Examples* (Jiang, Diao, Wang, Sun, Wang, Hong) | **Ataque a la protección** (purificación con difusión) | ACM MM '23 (DOI 10.1145/3581783.3611833) |
| **P23** | *Unmasking the Threat: Detecting Cloaking Attacks in Facial Recognition* (Merino, Zhou, King) | **Detección** de imágenes con *cloaking* (Fawkes) | No visible (formato IEEE) |
| **P24** | *Wavelet Domain Steganography for Robust Unlearnable Data* (WDS; Li, Chen, Xing, Gu) | **Protección** (sin modelo sustituto) | *Pattern Recognition* (número especial "PR_Security and Trustworthiness") |
| **P25** | *ARMOR: Shielding Unlearnable Examples against Data Augmentation* (Gong, Wang, Chen, Dong, Li, Sun, Li, Wang, Chen) | **Protección** robusta a *data augmentation* | No visible (formato de revista IEEE) |
| **P26** | *Clean-label poisoning attack with perturbation causing dominant features* (Zhang, Tang, Li) | **Protección/ataque** de envenenamiento (+ extensión a backdoor) | *Information Sciences* (Elsevier; DOI 10.1016/j.ins.2023.03.124) |

> **Ojo con la palabra "ataque" (igual que en el Informe 1).** Generar *unlearnable examples* es técnicamente un **ataque de envenenamiento** (*availability attack*) contra quien entrena, pero su propósito es **proteger** al dueño de los datos. Romper esa protección es una "defensa" para el entrenador y un **ataque a la privacidad** para el dueño. Uso: **Protección** = vuelve los datos no aprendibles; **Ataque (a la protección)** = los vuelve aprendibles otra vez; **Defensa/Detección** = técnica del entrenador para detectar o neutralizar la protección. P26 se llama a sí mismo "attack" pero su uso principal declarado es el de una protección (y además lo extiende a un *backdoor*, que sí es un ataque en sentido estricto).

### 0.2 Etiquetas epistemológicas (usadas en todo el informe)

- **[Afirma]** — lo que el paper *dice* (abstract, introducción, conclusiones), aunque no lo pruebe.
- **[Demuestra]** — lo que el paper *muestra con experimentos o con una prueba* incluida en el md (cito tabla/figura/teorema).
- **[Inferencia]** — lo que *yo deduzco* comparando cifras dentro de un paper o entre papers. Es razonamiento mío, no de los autores.
- **[No especificado]** — información necesaria para reproducir o interpretar un resultado que el md no proporciona.

### 0.3 Analogías

Las analogías aparecen marcadas con 🔎 **Analogía**. Inmediatamente después viene ✅ **Explicación técnica correcta**, porque toda analogía simplifica y puede inducir a error si se toma literalmente.

### 0.4 Convenciones numéricas

- Salvo que se diga otra cosa, las cifras son **exactitud sobre el test limpio (clean test accuracy, %)** del modelo entrenado con los datos indicados. En protecciones, **más bajo = mejor protección**; en ataques/defensas, **más alto = mejor recuperación**.
- Los presupuestos se escriben como en el paper: "8/255" (escala de píxeles 0–255) o "0.05" (escala 0–1). Para comparar: 8/255 ≈ 0.031; 16/255 ≈ 0.063; 0.05 ≈ 12.75/255.

---

## 1. Conceptos fundamentales (glosario explicado)

Esta sección explica, **la primera vez que aparecen**, los términos que se repiten en varios papers del bloque. Los términos de un único paper se explican dentro de su sección. Los conceptos básicos ya desarrollados en el Informe 1 se resumen aquí brevemente para que este informe sea autocontenido.

### 1.1 Unlearnable examples (UE) / unlearnable data (ULD) / availability attack

🔎 **Analogía.** Un alumno copia tus apuntes. Tú escribes en cada página una "pista" casi invisible que siempre coincide con la respuesta; el copión aprende a guiarse por la pista y en el examen real (sin pistas) falla.

✅ **Explicación técnica.** Un UE es un dato al que se añade una perturbación pequeña δ (‖δ‖ₚ ≤ ε) para que un modelo entrenado con él tenga **baja exactitud en datos limpios de prueba**, aunque su exactitud de entrenamiento sea alta. Se formula como **optimización binivel** (*bilevel*): el nivel interior entrena el modelo con los datos perturbados y el exterior busca δ que maximice el error en datos limpios (P22 Eq. 1; P24 Eq. 1; P26 Eq. 1). La literatura también lo llama **availability attack** (ataque a la disponibilidad: degrada la utilidad global del modelo, no una predicción concreta) (P21 §1; P22 §1).

### 1.2 Error-minimizing (EM/EMIN/EMinN) vs error-maximizing (EMAX/EMaxN, TAP)

🔎 **Analogía.** EM es "darle al alumno las respuestas tan fáciles que no estudia"; error-maximizing es "confundirlo con apuntes que parecen de otra materia".

✅ **Explicación técnica.**
- **Error-minimizing noise (EM)**: min_θ min_δ L(f_θ(x+δ), y). El ruido **reduce** la pérdida de entrenamiento: el modelo cree haber aprendido y deja de buscar rasgos semánticos (P24 Eq. 2; P25 Eq. 3). Distintos papers lo llaman EM (P16, P21, P24), EMIN (P25), EMinN (P20), EMN (P22) o "Unlearnable Examples [18]" (P26).
- **Error-maximizing / adversarial poisoning**: perturbaciones adversariales que **aumentan** la pérdida de un modelo ya entrenado (EMAX, EMaxN; **TAP**, *targeted adversarial poisoning*: hace que cada imagen "parezca" de una clase objetivo) (P25 §V.C; P22 §4.1).
- **Robust EM (REM / REMN)**: añade un adversario interno → min–min–max: min_θ min_δu max_δa L(f(x+δu+δa), y), para resistir **adversarial training** (P16 Eq. 5; P24 Eq. 3; P25 Eq. 4).

### 1.3 Shortcut learning (aprendizaje por atajos)

🔎 **Analogía.** Un detector de "vacas" que en realidad aprendió a detectar *pasto verde*, porque todas las fotos de vacas tenían pasto.

✅ **Explicación técnica.** Las redes aprenden primero la regla más simple que separa las clases. Si la perturbación está **correlacionada con la etiqueta**, se vuelve un atajo: el modelo la usa y no aprende la semántica. P21 lo usa para detectar UEs ("UEs make faster learners", §4.1); P24 lo formaliza con información mutua (Apéndice A.2–A.3); P26 lo describe como "dominant image features" (§3.1).

### 1.4 Sample-wise, class-wise y cluster-wise

- **Sample-wise**: cada imagen tiene su propia perturbación (EM sample-wise, ARMOR por defecto, P25 §V.B).
- **Class-wise**: todas las imágenes de una clase reciben la misma perturbación (o un patrón ligado a la clase). Es más fuerte pero más fácil de descubrir (P25 §VI.A cita esa desventaja de [19]).
- **Cluster-wise**: la misma perturbación para todos los ejemplos de un **cluster** encontrado sin etiquetas (P20).

🔎 **Analogía.** Sample-wise = una firma distinta en cada carta; class-wise = un sello idéntico por departamento; cluster-wise = un sello por "grupo de cartas que se parecen", aunque no sepas de qué departamento son.

✅ **Explicación técnica.** La diferencia determina cuán fácil es que el patrón funcione como atajo (class-wise lo es por diseño) y cuán transferible/detectable es.

### 1.5 Presupuesto (ε, norma Lₚ) e "imperceptible"

✅ **Explicación técnica.** ‖δ‖∞ ≤ ε limita el cambio máximo por píxel; ‖δ‖₂ limita la energía total; ‖δ‖₀ limita cuántos píxeles cambian (OPS cambia 1 píxel, P21 §5.1). El estándar en CIFAR es L∞ = 8/255. Algunos papers usan presupuestos mayores (UC: 16/255, P20; P26: 0.05 ≈ 12.75/255 en CIFAR-10) o **no reportan** un ε explícito (WDS, P24; TUE de video, P18), lo que dificulta comparar (ver §6).

### 1.6 Adversarial training (AT), PGD, FGSM y el radio ρ_a

🔎 **Analogía.** Entrenar a un boxeador haciéndolo pelear siempre contra el sparring que más daño le hace.

✅ **Explicación técnica.** AT resuelve min_θ max_‖δ_a‖≤ρ_a L(f(x+δ_a), y): en cada paso se genera un ejemplo adversarial (con **PGD**, *projected gradient descent*, iterativo, o **FGSM**, un solo paso) y se entrena con él. Es la defensa clásica contra UEs porque el ejemplo adversarial "rompe" el atajo de bajo error (P16 §1; P22 Eq. 2). **ρ_a** es el radio del atacante-entrenador; **ρ_u** el del protector. P16 recuerda que si ρ_a ≥ ρ_u la protección es inviable (Teorema 3 de su apéndice, mencionado en el md). Variantes vistas en este bloque: **Super-convergence** (basada en FGSM) y **Propagate** (basada en PGD) en P25; **AdvCL** (AT para aprendizaje contrastivo) en P22.

### 1.7 Surrogate model, surrogate-free y transferabilidad

✅ **Explicación técnica.** El protector no conoce el modelo del atacante; optimiza la perturbación contra un **modelo sustituto** (*surrogate*), p. ej. ResNet-18 (P16, P25), CLIP (P17, P20) o SiamFC (P18). **Surrogate-free** (P24 WDS) significa que la perturbación no se optimiza contra ningún clasificador víctima. **Transferabilidad** = que la protección siga funcionando contra arquitecturas o datasets distintos de los usados al generarla.

### 1.8 Partial poisoning / poison ratio / protección parcial

🔎 **Analogía.** Si solo el 20 % de los apuntes de una clase tiene la "pista" falsa, el alumno igual aprende del 80 % restante.

✅ **Explicación técnica.** El **poison ratio** es la fracción del dataset protegida. El Informe 1 ya mostró que con protección parcial la exactitud ≈ la de entrenar solo con la parte limpia. En este bloque, P19 ataca precisamente ese problema, y P16, P17, P20, P21, P24 y P25 reportan experimentos parciales (ver §4.4).

### 1.9 Generador (generative UE) e información lateral (*side information*)

🔎 **Analogía.** En vez de pintar a mano una "pista" en cada página, construyes una impresora que, al recibir una página, imprime automáticamente su pista.

✅ **Explicación técnica.** Un **generador** G (red encoder–decoder, U-Net, transformer, GAN) se entrena una vez para producir δ = G(x, ·); después generar ruido para una imagen nueva es solo una pasada hacia adelante (P15, P17, P18, P20). La **side information** es información adicional que condiciona al generador: la etiqueta codificada en bits (P15), un embedding de texto (P17), el estado de la *bounding box* (P18) o el código binario de clase (P24).

### 1.10 Contrastive learning, InfoNCE, CLIP y EMA

🔎 **Analogía.** Aprender a reconocer a tus amigos comparando: "esta foto y esta otra son de la misma persona; esta tercera no".

✅ **Explicación técnica.** El **aprendizaje contrastivo** acerca representaciones de pares positivos y aleja las de negativos. La pérdida **InfoNCE** es la forma estándar (P14 Eq. 5, temperatura τ=0.07; P17 Eq. 4). **CLIP** es un modelo imagen–texto entrenado contrastivamente; su *image encoder* f_I y *text encoder* f_T mapean ambos a un espacio común (P17 §3; P20 usa su image encoder como sustituto). **SimCLR, MoCo v2, BYOL** son marcos de aprendizaje auto-supervisado contrastivo/no contrastivo evaluados con **linear probing** (entrenar solo una capa lineal encima de las representaciones congeladas) (P22 §4.3). **EMA** (*exponential moving average*): una copia del modelo cuyos pesos son el promedio móvil de los del modelo principal, θ' = αθ' + (1−α)θ (P14 Eq. 4).

### 1.11 Data augmentation (DA) y augmentations "avanzadas"

✅ **Explicación técnica.** Transformaciones aplicadas a los datos de entrenamiento para generalizar mejor. Básicas: flip, crop, rotación. Avanzadas vistas en este bloque:
- **Cutout** (borra un parche), **Mixup** (combina linealmente dos imágenes y sus etiquetas), **CutMix** (pega un parche de otra imagen) (P22 Tabla 2; P24; P25).
- **PuzzleMix**: Mixup guiado por saliencia (P25 §II.B).
- **AutoAugment / Fast AutoAugment (FastAA) / DeepAA**: buscan automáticamente la política de augmentation; DeepAA maximiza la similitud coseno entre gradientes de datos originales y aumentados (P25 Eq. 5).
- **Feature distillation**: rediseño de la cuantización JPEG pensado contra ejemplos adversariales (P25 §II.B).
- **Grayscale, JPEG, bit-depth reduction**: compresiones que forman **ISS** (*Image Shortcut Squeezing*) (P22 Tabla 3).

### 1.12 Modelos de difusión, DDPM y purificación

🔎 **Analogía.** Ensuciar a propósito una foto con mucha "nieve" de TV hasta tapar una mancha pequeña, y luego pedirle a un restaurador experto que reconstruya la foto; el restaurador no sabe dibujar la mancha y la omite.

✅ **Explicación técnica.** Un **DDPM** (*Denoising Diffusion Probabilistic Model*) define un proceso hacia adelante que añade ruido gaussiano en T pasos y aprende el proceso inverso que elimina ruido (P22 Eq. 5–7). **Purificar** consiste en difundir la imagen protegida unos pocos pasos (T_p) para "sumergir" la perturbación y luego des-ruidarla con el modelo (P22 Eq. 8–9). **DiffPure** (usado en P21) y **AVATAR** (comparado en P22) son métodos de este tipo; **JCDP** es el de P22.

### 1.13 Métricas de detección: FAR, FRR, HTER, precision/recall/F1, ROC AUC

✅ **Explicación técnica.**
- P21 define **FAR = FP/(FP+TN)**, **FRR = FN/(FN+TP)** y **HTER = (FAR+FRR)/2** (P21 Eq. 5). HTER = 50 % equivale a un detector aleatorio.
- P23 usa **accuracy, precision** (de lo que marqué como "cloaked", cuánto lo era), **recall** (de lo que era "cloaked", cuánto marqué), **F1** (media armónica) y **ROC AUC** (área bajo la curva ROC; 0.5 = azar, 1 = perfecto).

### 1.14 Métricas de calidad visual: PSNR, SSIM, LPIPS, DSSIM

✅ **Explicación técnica.** **PSNR** (dB; mayor = menos distorsión; P26 Eq. 7), **SSIM** (similitud estructural, 0–1; mayor = mejor), **LPIPS** (distancia perceptual aprendida; menor = mejor; P15 Tabla 7, P24 Tabla D.11; P22 la usa además como *guía* de la purificación), **DSSIM** (disimilitud estructural; Fawkes exige DSSIM ≤ 0.007, P23 §II.A).

### 1.15 Clean-label, backdoor y ASR

✅ **Explicación técnica.** Un ataque **clean-label** no cambia las etiquetas: las imágenes envenenadas parecen correctamente etiquetadas a un humano (P23 §II.B; P26 §1). Un **backdoor** hace que el modelo se comporte normal salvo cuando la entrada contiene un **trigger**, en cuyo caso predice una clase objetivo; se mide con **ASR** (*attack success rate*: fracción de entradas no-objetivo con trigger clasificadas como la clase objetivo) (P26 §4.5). **Cloaking** (Fawkes) es una perturbación sobre fotos de rostro que, según P23, es una forma de clean-label poisoning.

---

## 2. Resumen completo y exhaustivo de los 13 papers

### 2.1 El problema común y cómo lo amplía este bloque

El problema de fondo es el mismo que en el Informe 1: **datos publicados (fotos, rostros, videos, pares imagen–texto) se recolectan sin permiso para entrenar modelos**, y los *unlearnable examples* intentan impedirlo modificando los datos antes de publicarlos. Este segundo bloque amplía la discusión en cuatro direcciones:

1. **Protecciones más robustas o más prácticas** frente a defensas concretas del entrenador:
   - contra **adversarial training**: P16 SEM, P24 WDS (y en parte P15 Segue, P25 ARMOR);
   - contra **data augmentation**: P25 ARMOR;
   - contra el **mezclado con datos limpios**: P19 Bayes error;
   - contra el **re-etiquetado** del dataset: P20 Unlearnable Clusters.
2. **Nuevos dominios y modos de uso**: rostros (P15, P23), **video/tracking** (P18), **multimodal imagen–texto** con una protección que **no necesita ver la imagen** (P17 T2UE).
3. **Detección**: detectar qué muestras de un dataset son UEs (P21) o qué fotos de rostro tienen *cloaking* de Fawkes (P23).
4. **Romper la protección o entrenar a pesar de ella**: purificación con difusión entrenada con datos no protegidos (P22 LE/JCDP) y aprendizaje robusto cuando además hay **ruido en las etiquetas** (P14 TPD).

P26 se ubica en la primera dirección (protección por "rasgos dominantes"), pero además muestra que la misma perturbación sirve como **trigger de backdoor**.

### 2.2 Resumen paper por paper (versión condensada)

**P14 — TPD (defensa).** Aborda un escenario doble: el dataset trae **perturbaciones adversariales de entrenamiento** (generadas con DeepConfuse) **y** **etiquetas ruidosas**. Combina:
- **aprendizaje contrastivo adversarial asimétrico**: genera ejemplos difíciles sin usar etiquetas;
- **selección de muestras** según su pérdida;
- **aprendizaje semi-supervisado**: usa la etiqueta solo en las muestras "limpias".

**[Demuestra]** Con ruido simétrico en CIFAR-10 logra 80.33/79.0/71.98 % para κ = 0.2/0.4/0.6, frente a 72.60/69.86/14.58 % de DivideMix (P14 Tabla 2). **[No especificado]** El presupuesto del ataque DeepConfuse y el rendimiento de TPD con datos limpios.

**P15 — Segue (protección facial generativa).** Un generador encoder–decoder produce la perturbación en **una pasada**. Usa como *side information* la etiqueta (16 bits) o pseudo-etiquetas de K-means, y una **capa de distorsión** durante el entrenamiento (AT ligero, blur, flips, nitidez).

**[Demuestra]**
- Exactitud de 10.50/2.50/11.50 % en WebFace10/WebFace50/VGGFace10 (limpio: 75.00/80.60/83.00; Tabla 3).
- Transferencia a 5 arquitecturas, incluida Inception-V3 (10.50 % frente a 56–77 % de los demás métodos; Tabla 5).
- 2.2 s para proteger WebFace10, frente a ~2.1k s de UE (Tabla 9).

**[Inferencia]** Su calidad visual (PSNR 30.54, SSIM 0.673) es **peor** que la de UE, RUE y LSP (Tabla 7).

**P16 — SEM (protección robusta a AT).** Descompone REM y **demuestra** que la robustez proviene del **modelo sustituto entrenado con AT**, no de entrenar el ruido contra perturbaciones adversariales (Tabla 1: REM-θ 37.90 % vs REM-δᵘ 88.28 %). Propone entrenar el ruido defensivo frente a **ruido aleatorio uniforme** δʳ ~ U(−ρ_r, ρ_r).

**[Demuestra]**
- CIFAR-10 con AT 4/255: 31.92 %, frente a 48.16 % de REM (Tabla 2).
- 3.91× más rápido que REM: 5.78 h vs 22.6 h (Tabla 15).

**[Demuestra]** Falla en CIFAR-100 con VGG-16 (57.11 % ≈ limpio 57.14 %) y con DenseNet-121 (55.36 %, por encima del limpio 53.73 %) (Tabla 3).

**P17 — T2UE (protección multimodal desde texto).** Un generador condicionado por el **embedding de texto de CLIP** produce ruido unlearnable **sin acceder a la imagen** ("zero-contact"). Se entrena minimizando el InfoNCE de CLIP entre la imagen perturbada y su texto.

**[Demuestra]**
- CLIP en Flickr30k: Hit@10 Imagen→Texto de 18.2 %, frente a 49.3 % sin protección (Tabla 2).
- CIFAR-10/100 supervisado (class-wise): 10.02 % / 1.08 % (Tabla 3).

**[Demuestra]** EM, que sí accede a la imagen, protege **más** que T2UE en los tres datasets multimodales (p. ej., 5.5 % en Flickr30k).

**P18 — Temporal UE (protección de video).** Primer UE para **visual object tracking (VOT)**:
- un generador **DiT** condicionado por la *bounding box* produce ruido "consciente del objetivo";
- una **pérdida contrastiva temporal** lo hace consistente a lo largo del video.

**[Demuestra]**
- SeqTrack en GOT-10k: AO de 74.7 → 2.1 (Tabla 4).
- Sigue protegiendo en un dataset no visto (LaSOT, AO 4.1; Tabla 5).
- Generador 4× más rápido de entrenar y 28× más pequeño que el baseline EM+Context (7 h / 124 MB vs 33 h / 3.4 GB; Tabla 2).

**[No especificado]** El presupuesto de perturbación; no evalúa ninguna defensa.

**P19 — Optimización del Bayes error (protección con garantía).** En lugar de crear un atajo, **maximiza una estimación del Bayes error** (el error mínimo inevitable de *cualquier* clasificador). La estimación es kernel y la maximización usa *projected gradient ascent* en el espacio de embeddings de ResNet-18.

**[Demuestra]** Que el estimado no disminuye (Teorema 3.4, bajo supuestos de suavidad).

**[Demuestra]** Con mitad UE + mitad limpia logra 69.68 %, frente a 91.16 % al entrenar solo con la mitad limpia y 92.51 % al añadir EM (texto de §1/RQ2). Con todo el dataset protegido obtiene 28.12 %, menos fuerte que otros UEs.

**P20 — Unlearnable Clusters (protección label-agnostic).** Muestra que los UEs existentes **dejan de funcionar si el atacante usa etiquetas distintas** de las del protector (p. ej., 19.93 → 93.77 % para EMinN; Fig. 2a). UC agrupa las imágenes con K-means sobre las *features* de un sustituto (ResNet-50 o **CLIP**) y entrena, por cluster, un generador que empuja todas sus imágenes hacia el centro de **otro** cluster (ε = 16/255).

**[Demuestra]**
- Pets con ResNet-18: 12.21 % con UC y 4.69 % con UC-CLIP (limpio 62.31 %; Tabla 1).
- Funciona contra Azure y PaddlePaddle (Tabla 2).

**[No especificado]** No evalúa AT.

**P21 — Iterative Filtering (detección).** Explota que los **UEs se aprenden más rápido** que los datos limpios. Entrena repetidamente un clasificador con la mitad del dataset (con *early stopping*), mueve las muestras identificadas como limpias a **clases adicionales** (y+C) e itera; luego hace un paso de "recuperación".

**[Demuestra]**
- HTER promedio en CIFAR-10 de 17.5 %, frente a 38.5 % de Deep k-NN y 50.1 % de TeCo (Tabla 1).
- **Falla** con 20 % de veneno para LSP, REM y AR (HTER 40.0/46.2/49.0).
- Aplicar DiffPure solo a lo detectado mejora la exactitud final (Tabla 5).

**P22 — Learnable Examples (ataque a la protección).** Argumenta que los UEs dan una **falsa sensación de seguridad**: el atacante puede juntar **otros datos no protegidos** (pocos, sin etiquetas, incluso de otra distribución), entrenar un DDPM y **proyectar los UEs al manifold de datos aprendibles**. El método **JCDP** guía la des-difusión con una distancia de píxel (MSE) y una perceptual (tipo LPIPS) respecto de la imagen protegida.

**[Demuestra]**
- CIFAR-10: entre 90.8 y 94.0 % contra EMN, EMN(C), REMN, LSP y TAP (limpio 95.3 %; Tabla 1).
- Rompe también UEs no supervisados (CP; UC/UC-CLIP en Pets: 46.0/47.5 % vs limpio 48.3 %; Tabla 4).
- Funciona aunque el DDPM se entrene con SVHN para purificar CIFAR-10 (89.3 % EMN; Tabla 5).

**P23 — Detección de cloaking facial.** Detecta fotos protegidas con **Fawkes** usando forense digital:
- **ELA** (*Error Level Analysis*);
- extracción de *features* con **img2vec/ResNet-18**;
- **PCA** y un **SVM**.

**[Demuestra]** En PubFig83 (5-fold CV) obtiene una exactitud de 93.04 % (nivel bajo), 99.24 % (medio) y 99.60 % (alto), y 97.44 % al mezclar niveles (Tablas I–IV).

**[No especificado]** Sin baselines ni otros algoritmos de cloaking.

**P24 — WDS (protección "surrogate-free").** Codifica la etiqueta en un **código binario** y lo "esteganografía" con autoencoders en las sub-bandas **cA** (baja frecuencia) y **cD** (diagonal) de la **transformada wavelet de Haar**, entrenando con pérdidas de reconstrucción y de extracción del código.

**[Demuestra]**
- CIFAR-10: 11.36 % con ERM y 15.27 % con AT 4/255, frente a 44.40 % de CUDA (Tabla 1).
- Resiste el ataque de frecuencia RSKFF: 28.73 % vs 78.53 % de CUDA (Tabla 5).

**[Inferencia]**
- No es el mejor bajo ERM en CIFAR-100 ni en ImageNet-100 (EM: 1.60 y 1.26 %, frente a 1.62 y 2.47 % de WDS).
- No reporta un presupuesto ε.

**P25 — ARMOR (protección robusta a data augmentation).** **[Demuestra]** La *data augmentation* avanzada **rompe** los UEs: EMIN pasa de 21.38 a 66.14 % con DeepAA (Tabla I). ARMOR integra **augmentation sustituta** dentro de la generación del ruido, con tres piezas:
- política elegida por clase maximizando la similitud de gradientes;
- un **módulo non-local** en el sustituto;
- un **paso adaptativo** en PGD.

**[Demuestra]** Con DeepAA en CIFAR-10 queda en 28–31 %, frente a 41–50 % de REM (Tabla V).

**[Inferencia]** Bajo AT es peor que REM en 9 de 12 configuraciones (Tabla XVIII), pese a que el abstract dice que "es robusto a AT".

**P26 — Dominant image features (clean-label poisoning).** Calcula, con un modelo entrenado en limpio, una **perturbación universal por clase** r_t que hace que cualquier imagen se clasifique como t (pérdida tipo C&W). Luego suma a cada clase c los "rasgos dominantes" de **otra** clase t.

**[Demuestra]**
- CIFAR-10: 10.64 % promedio en 6 arquitecturas (limpio 92.12 %); ImageNet-100: 1.80 % (Tabla 4).
- 10.54 % frente a 13.54–28.54 % de LSP, REM, EM y DeepConfuse con ε = 0.05 (Tabla 5).
- Resiste AT (19.75 %) y augmentations (Tabla 6).
- Como backdoor clean-label, ASR de 84.6 % (Tabla 8).

**[Inferencia]** ε = 0.05 equivale a ≈12.75/255, mayor que el estándar 8/255.

### 2.3 Cifras clave en una sola tabla

| Paper | Escenario representativo | Resultado del método | Referencia más relevante | Fuente |
|---|---|---|---|---|
| P14 TPD | CIFAR-10, DeepConfuse + ruido simétrico κ=0.6 | 71.98 % | DivideMix 14.58 % | Tabla 2 |
| P15 Segue | WebFace10 (ResNet-18) | 10.50 % | Limpio 75.00 %; UE 12.50 % | Tabla 3 |
| P16 SEM | CIFAR-10, AT 4/255 | 31.92 % | REM 48.16 %; limpio-AT 89.51 % | Tabla 2 |
| P17 T2UE | Flickr30k, CLIP, Hit@10 I→T | 18.2 | Sin protección 49.3; EM 5.5 | Tabla 2 |
| P18 TUE | SeqTrack, GOT-10k AO | 2.1 | Limpio 74.7; EM 8.1 | Tabla 4 |
| P19 Bayes | CIFAR-10, 50 % UE + 50 % limpio | 69.68 % | Solo 50 % limpio 91.16 %; +EM 92.51 % | §RQ2 |
| P20 UC | Pets, ResNet-18 | UC 12.21 % / UC-CLIP 4.69 % | Limpio 62.31 %; mejor baseline (AdvPoison) 50.86 % | Tabla 1 |
| P21 IF | CIFAR-10, HTER promedio | 17.5 % | Deep k-NN 38.5 %; TeCo 50.1 % | Tabla 1 |
| P22 LE | CIFAR-10, REMN | 92.2 % | AT 49.2 %; ISS 92.8 %; AVATAR 88.5 % | Tabla 1 |
| P23 ELA+SVM | PubFig83, niveles mezclados | Acc 97.44 %, AUC 0.9916 | — (sin baseline) | Tabla IV |
| P24 WDS | CIFAR-10, AT 4/255 | 15.27 % | CUDA 44.40 %; SEM 31.92 % | Tabla 1 |
| P25 ARMOR | CIFAR-10, DeepAA, ResNet-18 | 28.84 % | REM 43.25 %; EMIN 56.05 % | Tabla V |
| P26 Dominant | CIFAR-10, RegNet, ε=0.05 | 10.54 % | LSP 13.54 %; REM 15.18 % | Tabla 5 |

### 2.4 Hallazgos transversales (lo que emerge al leerlos juntos)

1. **La "robustez" siempre es relativa a una defensa concreta.**
   - SEM (P16) resiste AT, pero no fue probado contra augmentations avanzadas ni contra purificación.
   - ARMOR (P25) resiste augmentations, pero bajo AT es peor que REM en la mayoría de configuraciones.
   - WDS (P24) resiste AT y RSKFF, pero no fue probado contra difusión.
   - UC (P20) resiste el re-etiquetado, pero no fue probado con AT.

   **[Inferencia]** Ningún paper del bloque evalúa su protección contra el ataque de P22 (difusión entrenada con otros datos), que según P22 rompe EMN, REMN, LSP, TAP, CP y UC.
2. **La protección parcial sigue siendo el talón de Aquiles**, pero empieza a atacarse explícitamente:
   - **P19 Bayes error**: [Demuestra] reduce la exactitud por debajo de la del entrenamiento con solo la parte limpia.
   - **P24 WDS**: en ERM con 80 % protegido logra 71.33 % frente a 84.30 % del subconjunto limpio solo; [Inferencia] los datos protegidos llegan a *dañar*.

   Esto contrasta con la conclusión del Informe 1 (protegidos "no aportan pero no dañan").
3. **Los atajos son detectables precisamente porque son atajos.** P21 usa que se aprenden más rápido; P20 (Fig. 6b) muestra que el modelo aprende la distribución del ruido; P16 y P24 los describen como patrones "monótonos" o de baja varianza intra-clase. **[Inferencia]** La propiedad que hace eficaz a un UE (ser fácil de aprender) es la misma que lo hace detectable.
4. **Generadores en todas partes.** P15, P17, P18 y P20 entrenan generadores. La ventaja que reportan es la velocidad de *inferencia*: 2.2 s (P15), 0.3 h (P17) y 7 h de entrenamiento (P18). **[Inferencia]** Varios no reportan el costo de entrenar el generador (P15) o lo excluyen de la comparación (P17).
5. **El dominio cambia la dificultad.** Proteger video (P18) y pares imagen–texto (P17) exige mecanismos nuevos (condición en *bounding box*, contraste temporal, condición textual). Los rostros reaparecen en P15, P23 y P26 (caso PubFig).
6. **Cifras de baselines repetidas entre papers.**
   - P24 reproduce exactamente las cifras de P16 para REM y SEM (48.16 / 31.92 % en CIFAR-10 con AT) y las de CUDA del Informe 1 (18.48 / 44.40 %).
   - P26 reporta REM = 15.18 % en CIFAR-10, una cifra que en P16 corresponde a REM **sin AT** (Tabla 2, ρ_a = 0).

   **[Inferencia]** Hay reutilización de números de la literatura, lo que obliga a leer con cuidado las comparaciones (ver §6).

---

## 3. Informe detallado por paper

Cada paper sigue la misma plantilla del Informe 1: (1) problema, (2) propuesta, (3) funcionamiento técnico, (4) datos de entrada y salida, (5) evaluación, (6) resultados e interpretación, (7) caso aplicado, (8) fortalezas y limitaciones, (9) cuadro "afirma / demuestra / inferencia / no especificado", (10) relación con los demás papers.

---

### P14 · TPD (Two-Pronged Defense)

**Título:** *Robust Learning with Adversarial Perturbations and Label Noise: A Two-Pronged Defense Approach*. **Autores:** Peng-Fei Zhang, Zi Huang (Univ. of Queensland), Xin Luo, Pengfei Zhao (Shandong Univ.). **Publicación:** ACM MMAsia '22. **Rol:** defensa (aprendizaje robusto).

#### P14.1 Problema investigado
Los datasets reales pueden estar contaminados **a la vez** por:
- **perturbaciones adversariales en todo el training set**, un *training-time attack* tipo "Learning to Confuse" (DeepConfuse) que degrada el modelo, al estilo de un UE;
- **etiquetas ruidosas** (*label noise*), típicas del crowdsourcing.

Los métodos existentes tratan solo uno de los dos problemas. **[Afirma]** Es "el primer trabajo" que aborda ambos simultáneamente (P14 §1).

**Label noise** — 🔎 *Analogía:* un profesor que corrige exámenes y se equivoca en el 20 % de las notas. ✅ *Técnico:* una fracción κ de las etiquetas de entrenamiento es incorrecta. El paper evalúa tres tipos:
- **simétrico**: la etiqueta se cambia por cualquier otra al azar;
- **asimétrico**: se cambia por una clase "parecida", p. ej. deer→horse o dog→cat;
- **instance-dependent**: la probabilidad de error depende de la imagen.

#### P14.2 Propuesta
**TPD** ("defensa de dos frentes") con tres módulos:
1. **Asymmetric adversarial contrastive learning**: genera ejemplos adversariales sin usar etiquetas.
2. **Sample selection**: separa muestras probablemente limpias de las ruidosas.
3. **Semi-supervised learning**: usa la etiqueta solo en las limpias.

#### P14.3 Funcionamiento técnico
- **Arquitectura:** modelo f_θ = encoder g + clasificador h, y una copia **EMA** f_θ' que actúa como *key network* (θ' = αθ' + (1−α)θ, α = 0.999; Eq. 4).
- **Contraste adversarial asimétrico (Eq. 5):** una **función de perturbación ρ** aprende a **maximizar** la pérdida InfoNCE (τ = 0.07) entre g(ρ(x^q)) y g'(x^k) (vista de la red EMA).
  - Como InfoNCE **no usa etiquetas**, los ejemplos adversariales "de peor caso" no se ven afectados por el ruido de etiquetas.
  - El clasificador aprende a predecir igual sobre la imagen original y la adversarial.
  - "Asimétrico" significa que la perturbación se entrena con la pérdida contrastiva y el clasificador con la semi-supervisada.
- **Selección de muestras:** una muestra se considera **ruidosa** si su pérdida supera el umbral μ = pérdida promedio del modelo EMA en todo el dataset. Así se forman D_cln y D_noy.
- **Semi-supervisado (Eq. 6):**
  - L_cln = entropía cruzada (CE) sobre x, aug(x) y λ·ρ(x) (λ = 0.1);
  - L_nsy = MSE contra la predicción promedio M = ½(f_θ(x) + f_θ'(x)), es decir, **descarta la etiqueta** ruidosa;
  - L_reg = KL contra una distribución uniforme (evita colapsar a una clase).
- **Entrenamiento (Alg. 1):** *warm-up* (T1 épocas) + entrenamiento formal (T2):
  - CIFAR-10: 10 + 90 épocas; CIFAR-100: 30 + 70;
  - backbone ResNet34;
  - radio de ρ: ε = 0.1 (CIFAR-10) y 0.2 (CIFAR-100);
  - augmentation fuerte: AutoAugment.

🔎 **Analogía.** Un estudiante que no confía en las notas del profesor (pueden estar mal) entrena con "preguntas trampa" que él mismo inventa sin mirar las respuestas oficiales. Solo usa las respuestas oficiales en los ejercicios donde su intuición coincide con ellas.

✅ **Explicación técnica correcta.**
- Las "preguntas trampa" son las perturbaciones ρ(x) que maximizan InfoNCE.
- "Su intuición coincide" corresponde a una pérdida por muestra menor que el promedio del modelo EMA.
- En las muestras sospechosas se reemplaza la etiqueta por la predicción promediada (MSE).

#### P14.4 Datos que necesita y resultado que produce
- **Entrada:** dataset etiquetado (con etiquetas posiblemente erróneas) cuyas imágenes pueden estar perturbadas. No necesita saber qué muestras están perturbadas o mal etiquetadas.
- **Salida:** un clasificador entrenado que generaliza al test limpio.

#### P14.5 Cómo fue evaluado
- **Datos:** CIFAR-10 y CIFAR-100. Perturbaciones generadas con DeepConfuse sobre todo el training set; ruido de etiquetas simétrico, asimétrico e instance-dependent con tasa κ.
- **Baselines:** CE estándar; AT (Tao et al.); DAT (alineación de distribuciones); CORES² (*sample sieve*); DivideMix (mezcla gaussiana + semi-supervisado).
- **Configuraciones:** C (limpio), AP (solo perturbaciones), LN (solo ruido de etiquetas), AP&LN (ambos).

#### P14.6 Resultados clave e interpretación

**Tabla P14-a (CIFAR-10, exactitud %; Tablas 1–3 de P14).**

| Escenario | CE | AT | DAT | CORES² | DivideMix | **TPD** |
|---|---|---|---|---|---|---|
| AP&LN instance κ=0.2 | 40.76 | 37.98 | 27.78 | 14.18 | 75.33 | **79.06** |
| AP&LN instance κ=0.4 | 14.90 | 13.29 | 17.25 | 14.62 | 16.17 | **42.47** |
| AP&LN instance κ=0.6 | 12.75 | 10.36 | 10.48 | 14.43 | 24.74 | **25.22** |
| AP&LN simétrico κ=0.2 | 26.89 | 25.75 | 29.74 | 14.08 | 72.60 | **80.33** |
| AP&LN simétrico κ=0.4 | 18.59 | 22.94 | 32.13 | 14.30 | 69.86 | **79.0** |
| AP&LN simétrico κ=0.6 | 17.79 | 10.14 | 29.22 | 13.15 | 14.58 | **71.98** |
| AP&LN asimétrico κ=0.2 | 35.52 | 48.05 | 27.40 | 14.59 | 72.50 | **76.32** |
| AP&LN asimétrico κ=0.4 | 21.64 | 30.55 | 28.44 | 13.70 | 27.25 | **60.32** |

Referencias de la Tabla 1 (CE, CIFAR-10):
- limpio: 95.20 %;
- solo AP: 62.91 %;
- solo LN (instance κ = 0.2/0.4/0.6): 87.50/74.85/45.23 %;
- AT con solo AP: 87.50 %.

**Tabla P14-b (CIFAR-100, AP&LN).**

| Escenario | DivideMix | **TPD** |
|---|---|---|
| instance κ=0.2 / 0.4 / 0.6 | 48.49 / 42.65 / 21.17 | 51.41 / 46.06 / 24.67 |
| simétrico κ=0.2 / 0.4 / 0.6 | 49.52 / 45.80 / 33.87 | 54.68 / 46.83 / 36.18 |
| asimétrico κ=0.2 / 0.3 / 0.4 | 49.77 / 45.06 / 33.85 | 53.12 / 47.18 / 38.86 |

**Interpretación.**
- **[Demuestra]** La combinación AP&LN es mucho más dañina que cada problema por separado:
  - CE en CIFAR-10 cae 33.90 puntos con AP, 16.90 con LN y 75.0 con ambos (texto de §5);
  - los métodos especializados en un solo problema colapsan con el otro. Por ejemplo, CORES² rinde 78.43 % con solo LN instance κ = 0.6 pero 14.43 % con AP&LN.
- **[Demuestra]** TPD es el mejor en todas las configuraciones AP&LN reportadas. La ventaja es grande con ruido simétrico (71.98 vs 14.58 en κ = 0.6) y marginal en algunos casos (instance κ = 0.6: 25.22 vs 24.74).
- **[Inferencia]** En CIFAR-100 TPD queda entre 24.67 y 54.68 %, lejos del limpio (CE 66.48 %): el problema conjunto sigue abierto.
- **[Inferencia]** AT casi no ayuda en CIFAR-100 con solo AP (29.28 % vs 25.01 % de CE). El paper no lo comenta.

#### P14.7 Caso aplicado
*Una empresa compra a un proveedor externo 50 000 imágenes etiquetadas por crowdsourcing; sin saberlo, un competidor envenenó todas las imágenes con DeepConfuse y el 40 % de las etiquetas están mal (ruido simétrico).*
- Con entrenamiento estándar el modelo llega a 18.59 %.
- Con DivideMix, el mejor método de label noise, llega a 69.86 %.
- Con TPD llega a 79.0 % (CIFAR-10, Tabla 2).

**Lectura práctica:** cuando no se sabe qué está mal en los datos, conviene una defensa que no confíe en las etiquetas para generar ejemplos difíciles. Aun así, si el problema es más difícil (100 clases), la exactitud sigue siendo baja.

#### P14.8 Fortalezas y limitaciones
- **Fortalezas:**
  - aborda un escenario realista combinado;
  - tres tipos de ruido y dos datasets;
  - componentes justificados (InfoNCE sin etiquetas evita que el ruido de etiquetas contamine la generación adversarial).
- **Limitaciones declaradas:** ninguna sección explícita de limitaciones (paper corto, 7 páginas).
- **Limitaciones inferidas:**
  - **[No especificado]** el presupuesto ε del ataque DeepConfuse;
  - **[No especificado]** el rendimiento de TPD en C, AP-solo y LN-solo, por lo que no se conoce su costo en datos limpios;
  - **[Inferencia]** solo un tipo de ataque (DeepConfuse), sin varianza ni repeticiones;
  - el radio ε del propio TPD (0.1 / 0.2 en L∞) es grande;
  - **[Inferencia]** el caption de la Tabla 1 es confuso respecto de qué baselines se reportan en qué columnas;
  - el weight decay aparece como "15e-4", un valor inusual.

#### P14.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Primer trabajo en tratar perturbaciones adversariales y ruido de etiquetas a la vez; TPD mejora en promedio 20.95 % sobre los baselines. |
| **Demuestra** | Mejor exactitud en todas las configuraciones AP&LN de las Tablas 1–3 (CIFAR-10/100). |
| **Inferencia** | Ventaja marginal en casos de ruido instance alto; CIFAR-100 sigue muy por debajo del limpio; AT no ayuda en CIFAR-100. |
| **No especificado** | Presupuesto de DeepConfuse; desempeño en datos limpios o con un solo problema; varianza. |

#### P14.10 Relación con los demás papers
- Es el único paper del bloque que combina **UE/availability attack + label noise**.
- Comparte con **P22** el objetivo de "aprender a pesar del veneno", pero P22 **limpia los datos** (independiente del entrenamiento), mientras TPD **cambia el entrenamiento**.
- Su uso de **aprendizaje contrastivo** como componente defensivo recuerda a **VESPR** (Informe 1, P7), que combina SSL + SL + AT. **[Inferencia]** Ambos sugieren que las señales auto-supervisadas son menos vulnerables a los atajos ligados a la etiqueta.
- DeepConfuse aparece como baseline de protección en P20 (Tabla 1) y P26 (Tabla 5). En P26 deja 28.54 % en CIFAR-10, coherente con que es un veneno eficaz contra el entrenamiento estándar.

---

### P15 · Segue

**Título:** *Segue: Side-information Guided Generative Unlearnable Examples for Facial Privacy Protection in Real World*. **Autores:** Zhiling Zhang, Jie Zhang (NTU), Kui Zhang, Wenbo Zhou, Weiming Zhang, Nenghai Yu (USTC). **Rol:** protección de rostros.

#### P15.1 Problema investigado
Proteger fotos de rostros contra el entrenamiento no autorizado de sistemas de reconocimiento facial (FR). Los UEs existentes no cumplen a la vez cinco requisitos que los autores consideran necesarios:
- **Effectiveness**, **Imperceptibility**, **Transferability**, **Robustness**, **Efficiency** (P15 Tabla 1).

En su comparación, UE, LSP, RUE y TUE (*Transferable* UE) cumplen solo algunos.

#### P15.2 Propuesta
Un **generador** que produce la perturbación en una sola pasada, guiado por **side information**:
- la **etiqueta** codificada en binario de 16 bits (hasta 2¹⁶ identidades);
- en el caso sin etiquetas, **pseudo-etiquetas** de K-means sobre *features* de un extractor entrenado en CelebA.

Se añade una **capa de distorsión** durante el entrenamiento para dar robustez.

🔎 **Analogía.** En lugar de disfrazar a mano a cada persona de una foto, una máquina de disfraces recibe la foto y un "número de identidad" y produce un disfraz casi invisible que siempre está asociado a ese número.

✅ **Explicación técnica correcta.** G (encoder–decoder conv 3×3 + BN + ReLU) fusiona en un espacio de alto nivel el embedding de la imagen con el de la etiqueta (concatenación de canales + conv). La salida δ = clip(G(x), −ε, ε), con ε = 8/255, queda correlacionada con la identidad: es un atajo class-wise aprendido. El modelo FR aprende "este patrón = esta identidad" y no los rasgos faciales.

#### P15.3 Funcionamiento técnico
- **Separabilidad lineal como guía:** citando LSP, los autores asumen que perturbaciones class-wise **linealmente separables** son más transferibles. La side information busca aumentar esa separabilidad.
- **Capa de distorsión:**
  - AT con ρ_d = 1/255 por defecto;
  - Gaussian blur (k = 3, σ = 0.2);
  - flips horizontales y verticales (p = 0.1);
  - nitidez (factor 2).

  Los autores la motivan con que "AT ~ augmentation", en contraste con el min–min–max de RUE.
- **Entrenamiento alterno (Alg. 1):**
  - sustituto f (ResNet-18) con L_f = CE(f(x+G(x)), ŷ);
  - generador con L_G = α·CE(f(x+G(x)), ŷ) + β·E‖G(x)‖₂, con α = 1 y β = 0.001. Es un esquema **error-minimizing generativo** (min–min) con un término que penaliza el tamaño del ruido;
  - Adam con lr 5e-4; se detiene tras 20 épocas o cuando la pérdida de f < 0.001.

#### P15.4 Datos y resultado
- **Entrada:** imágenes de rostros + identidad (o el número K de identidades si no hay etiquetas); sustituto ResNet-18.
- **Salida:** imágenes protegidas.
- **Datasets (Tabla 2):**
  - WebFace10 (10 IDs; 1300 train / 200 test) y WebFace10† (10 IDs disjuntos);
  - WebFace50 (50 IDs; 6500/1300);
  - VGGFace10 (1300/200) y CelebA10 (200/100);
  - CIFAR-10 e ImageNet10.

#### P15.5 Evaluación
- **Métrica:** exactitud limpia (menor = mejor).
- **Baselines:** UE, LSP, RUE (*Robust* UE = REM) y TUE.
- **Pruebas:**
  - transferencia a 5 arquitecturas (Tabla 5) y a otros datasets (Tabla 6);
  - imperceptibilidad: PSNR, SSIM, LPIPS (Tabla 7);
  - robustez a AT (Tabla 8), y a JPEG y augmentations (Fig. 4);
  - tiempo (Tabla 9).

#### P15.6 Resultados clave e interpretación

**Tabla P15-a (ResNet-18, exactitud %; P15 Tablas 3–4).**

| Dataset | Limpio | UE | LSP | RUE | TUE | **Segue** |
|---|---|---|---|---|---|---|
| WebFace10 | 75.00 | 12.50 | 31.50 | 11.50 | 33.50 | **10.50** |
| WebFace50 | 80.60 | 3.50 | 9.30 | 7.40 | 11.20 | **2.50** |
| VGGFace10 | 83.00 | 20.50 | 57.50 | 30.00 | 82.00 | **11.50** |
| CIFAR-10 | 91.67 | 19.93 | 17.07 | 15.18 | 11.25 | **10.12** |
| ImageNet10 | 71.00 | 30.00 | 64.00 (ε=8) / 28.50 (ε=56) | 24.50 | 60.50 | **14.00** |

**Tabla P15-b (transferencia entre arquitecturas, WebFace10; P15 Tabla 5).**

| Método | ResNet-18 | ResNet-50 | MobileNet-V1 | Inception-V3 | EfficientNet-b1 |
|---|---|---|---|---|---|
| UE | 14.50 | 14.50 | 15.50 | 73.00 | 28.00 |
| RUE | 19.00 | 27.50 | 17.00 | 77.00 | 27.00 |
| TUE | 33.50 | 70.00 | 15.50 | 69.00 | 67.50 |
| **Segue** | **10.50** | **12.50** | **11.00** | **10.50** | **12.00** |

**Tabla P15-c (robustez a AT, WebFace10, exactitud % según ρ_a del atacante = 0/1/2/3/4 ×1/255; P15 Tabla 8).**

| Método | 0 | 1 | 2 | 3 | 4 |
|---|---|---|---|---|---|
| Limpio | 75.00 | 68.00 | 65.00 | 63.50 | 65.50 |
| UE | 12.50 | 18.00 | 69.00 | 74.50 | 69.50 |
| RUE (ρ_d=4) | 14.50 | 17.50 | 22.50 | 58.00 | 63.00 |
| Segue (ρ_d=0) | 10.50 | 13.50 | 15.50 | 29.00 | 34.00 |
| **Segue (ρ_d=4)** | 13.50 | 12.50 | 12.50 | 14.50 | **16.50** |

**Interpretación.**
- **[Demuestra]** Segue es el más efectivo en todos los datasets de la Tabla 3–4. Su mayor ventaja es la **transferencia**: es el único que sigue funcionando en Inception-V3 (10.50 % vs 56–77 %).
- **[Demuestra]** Con la capa de distorsión (ρ_d = 4) resiste AT hasta ρ_a = 4/255 (16.50 %), mientras RUE sube a 63.00 %.
- **[Demuestra]** Tiempo de protección de WebFace10 (A6000): Segue 2.2 s; UE ~2.1k s; RUE ~6.7k s; TUE ~7.4k s; LSP 4.5 s (Tabla 9).
- **[Inferencia]** Ese tiempo es de *inferencia* del generador; el md **no reporta** cuánto cuesta entrenarlo.
- **[Inferencia] Imperceptibilidad (Tabla 7, WebFace10, PSNR↑ / SSIM↑ / LPIPS↓):**

  | Método | PSNR | SSIM | LPIPS |
  |---|---|---|---|
  | UE | 32.37 | .754 | .205 |
  | LSP | 31.53 | .968 | .049 |
  | RUE | 32.45 | .763 | .188 |
  | TUE | 30.18 | .651 | .310 |
  | Segue | 30.54 | .673 | .159 |

  Segue es **peor** que UE, RUE y LSP en PSNR/SSIM y mejor en LPIPS que UE/RUE/TUE; LSP es mejor en las tres métricas. El paper lo califica de "comparable".
- **[Demuestra/Afirma]** La Fig. 4 indica robustez a JPEG y augmentations (blur, cutout, mixup, cutmix), y que la side information con etiquetas reales transfiere mejor que con pseudo-etiquetas. **[No especificado]** Los valores numéricos de la Fig. 4 no están en el md.

#### P15.7 Caso aplicado
*Una red social quiere ofrecer a sus usuarios un botón "proteger mis fotos" antes de publicarlas.*
- Con UE o RUE, proteger un álbum de 10 identidades tardaría del orden de horas (2.1k–6.7k s); con Segue, 2.2 s (Tabla 9).
- Si el recolector entrena una Inception-V3 en lugar de una ResNet, UE y RUE dejan de proteger (73–77 % de exactitud), mientras Segue mantiene ~10.5 % (Tabla 5).

**Matiz:** las fotos protegidas tienen algo más de distorsión visible que con UE (PSNR 30.54 vs 32.37), y todos los experimentos protegen **el dataset completo** de pocas identidades (10–50).

#### P15.8 Fortalezas y limitaciones
- **Fortalezas:**
  - cubre cinco requisitos con un mismo método;
  - transferencia entre arquitecturas y datasets;
  - robustez a AT ajustable con ρ_d;
  - versión no supervisada con pseudo-etiquetas;
  - muy rápido en inferencia.
- **Limitaciones inferidas:**
  - **[No especificado]** tiempo de entrenamiento del generador y valores de la Fig. 4;
  - **[Inferencia]** datasets muy pequeños (10–50 identidades, 200–1300 imágenes);
  - **[Inferencia]** todo el dataset está protegido: no se evalúa el escenario realista de pocos usuarios protegidos dentro de un dataset grande;
  - **[Inferencia]** inconsistencias en las cifras de RUE en WebFace10/ResNet-18 (11.50 en Tabla 3; 19.00 en Tabla 5; 17.00 en Tabla 6);
  - **[Inferencia]** el texto describe el calendario alterno de forma distinta al algoritmo ("update G four epochs" vs "f one epoch then G five epochs");
  - sin varianza.

#### P15.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Cumple los 5 requisitos (Tabla 1); robusto si la exactitud del clustering para pseudo-etiquetas es >80 %; imperceptibilidad "comparable". |
| **Demuestra** | Menor exactitud en todos los datasets (Tablas 3–4), transferencia (Tablas 5–6), AT hasta 4/255 con ρ_d=4 (Tabla 8), 2.2 s (Tabla 9). |
| **Inferencia** | Calidad visual inferior a UE/RUE/LSP; costo de entrenamiento omitido; escala pequeña. |
| **No especificado** | Tiempo de entrenamiento de G; cifras de JPEG/augmentations; venue. |

#### P15.10 Relación con los demás papers
- **Generador** como GUE (Informe 1, P8), T2UE (P17), TUE (P18) y UC (P20). Segue condiciona con la **etiqueta**; T2UE con **texto**; TUE con la **bounding box**; UC con el **cluster**.
- Rostros como PyrFawkes (Informe 1, P12) y la detección de Fawkes (P23). **[Inferencia]** P23 mostraría que un detector supervisado puede reconocer perturbaciones conocidas; Segue no evalúa detección.
- Usa K-means para pseudo-etiquetas, igual que **UC (P20)**, a quien cita.
- Su capa de distorsión con AT pequeño (ρ_d) es una alternativa barata al min–min–max de REM. **SEM (P16)** propone otra: ruido aleatorio en lugar de adversarial.

---

### P16 · SEM (Stable Error-Minimizing)

**Título:** *Stable Unlearnable Example: Enhancing the Robustness of Unlearnable Examples via Stable Error-Minimizing Noise*. **Autores:** Yixin Liu, Kaidi Xu, Xun Chen, Lichao Sun (Lehigh, Drexel, Samsung Research America). **Publicación:** AAAI 2024. **Rol:** protección robusta a AT.

#### P16.1 Problema investigado
REM (*robust error-minimizing noise*) resiste AT, pero es caro: ~22 h en CIFAR-10 (Tabla 1). Además, aplica AT en dos lugares: al **modelo sustituto θ** y al **ruido defensivo δᵘ**. Los autores preguntan: **¿de dónde viene realmente la robustez?**

#### P16.2 Propuesta
**SEM (Stable Error-Minimizing noise)**: mantener AT en el sustituto, pero entrenar el ruido defensivo contra **ruido aleatorio** en lugar de contra perturbaciones adversariales.

🔎 **Analogía.** Para preparar a un equipo contra cualquier rival, en vez de entrenarlo solo contra el rival más agresivo (que lo vuelve rígido y especializado), se lo entrena contra rivales variados al azar; así su estrategia es más estable.

✅ **Explicación técnica correcta.**
- La actualización del ruido es δᵘ ← δᵘ − η∇L(f(t(x+δᵘ) + δʳ), y), con δʳ ~ U(−ρ_r, ρ_r) y ρ_r = ρ_a (Eq. 10).
- El sustituto sigue entrenándose con PGD-AT (Eq. 5).
- Se promedian J muestras de (δʳ, t), donde t es una transformación, al estilo EOT (*Expectation over Transformation*).

#### P16.3 Funcionamiento técnico
- **Diagnóstico (Tabla 1, CIFAR-10, AT 4/255):**

  | Variante | Costo | Exactitud |
  |---|---|---|
  | REM completo (AT en θ y δᵘ) | 22 h | 46.72 % |
  | Solo AT en el ruido (REM-δᵘ) | 15 h | 88.28 % |
  | Solo AT en el sustituto (REM-θ) | 6 h | 37.90 % |
  | **SEM** | 6 h | **30.26 %** |

  ⇒ la robustez viene del **sustituto**; hacer AT en el ruido **empeora**.
- **Dos definiciones de robustez:** R_θ (robustez del sustituto) y R_δᵘ (robustez del ruido). La correlación con F = 1 − Acc es:
  - para R_θ: Pearson .9551, Spearman .8422, Kendall .6921;
  - para R_δᵘ: negativa (−.5851 / −.7003 / −.5407) (Fig. 3 y Tabla 10).
- **[Afirma]** Hipótesis: el ruido entrenado contra perturbaciones adversariales "monótonas" sobreajusta y se vuelve inestable.
- **Algoritmo (Alg. 1):** por minibatch, K_u pasos de signo-gradiente sobre δᵘ promediando J muestras; después PGD para δᵃ y actualización del sustituto. Finalmente se regenera el ruido con el generador entrenado (Alg. 4, apéndice).

#### P16.4 Datos y resultado
- **Entrada:** dataset etiquetado completo.
- **Configuración:**
  - ρ_u = 8/255, ρ_a = 4/255;
  - requiere suponer el radio de AT del atacante, ya que ρ_r = ρ_a;
  - sustituto ResNet-18.
- **Salida:** ruido sample-wise.

#### P16.5 Evaluación
- **Datos:** CIFAR-10, CIFAR-100 y subconjunto de ImageNet (100 clases).
- **Modelos:** ResNet-18, VGG-16, ResNet-50, DenseNet-121, WRN-34-10.
- **Baselines:** EM, TAP, NTGA, SC (*synthesized shortcuts* = LSP), REM.
- **Pruebas:** AT con ρ_a ∈ {0,…,4}/255; filtros paso-bajo; *early stopping*; Facescrub; protección parcial; otros ataques AT.
- **Repeticiones:** 3 semillas.

#### P16.6 Resultados clave e interpretación

**Tabla P16-a (exactitud % con AT de radio ρ_a = 0/1/2/3/4 ×1/255, ResNet-18; P16 Tabla 2).**

| Dataset | Método | 0 | 1 | 2 | 3 | 4 |
|---|---|---|---|---|---|---|
| CIFAR-10 | Limpio | 94.66 | 93.74 | 92.37 | 90.90 | 89.51 |
| | EM | 13.20 | 22.08 | 71.43 | 87.71 | 88.62 |
| | REM | 15.18 | 14.28 | 25.41 | 30.85 | 48.16 |
| | **SEM** | 13.0 | 12.16 | 11.49 | 20.91 | **31.92** |
| CIFAR-100 | REM | 1.89 | 4.47 | 7.03 | 17.55 | 27.10 |
| | **SEM** | 1.95 | 3.26 | 4.35 | 9.07 | **20.25** |
| ImageNet subset | REM | 13.74 | 21.58 | 29.40 | 35.76 | 41.66 |
| | **SEM** | 4.1 | 10.34 | 13.76 | 23.58 | **37.82** |

**Tabla P16-b (arquitecturas, AT 4/255; P16 Tabla 3).**

| Dataset | Modelo | Limpio | REM | SEM |
|---|---|---|---|---|
| CIFAR-10 | VGG-16 | 87.51 | 65.23 | 44.37 |
| CIFAR-10 | DenseNet-121 | 83.27 | 81.48 | 77.85 |
| CIFAR-100 | VGG-16 | 57.14 | 48.85 | **57.11** |
| CIFAR-100 | DenseNet-121 | 53.73 | 54.48 | **55.36** |
| CIFAR-100 | WRN-34-10 | 68.64 | 25.04 | 18.90 |

**Interpretación.**
- **[Demuestra]** SEM mejora a REM en todos los radios de la Tabla 2 salvo ρ_a = 0 en CIFAR-100 (1.95 vs 1.89), y es 3.91× más rápido:
  - CIFAR-10/100: 5.78 h vs 22.6 h;
  - ImageNet subset: 1.33 h vs 5.2 h (Tabla 15).
- **[Demuestra]** Ablación (Tabla 7):

  | Variante | Exactitud | Diferencia |
  |---|---|---|
  | SEM completo | 30.03 % | — |
  | Sin δʳ | 37.91 % | +7.88 |
  | Ruido entrenado con δᵃ | 46.72 % | +16.69 |
  | Sin AT en el sustituto | 89.22 % | +59.19 |

  La pieza clave es el sustituto robusto.
- **[Demuestra]** El mejor resultado se obtiene cuando ρ_r = ρ_a, la diagonal de la Tabla 6. Con ρ_a = 4: ρ_r = 0 → 89.30 %; ρ_r = 2 → 35.63 %; ρ_r = 3 → 62.09 %; ρ_r = 4 → 31.92 %. **[Inferencia]** El método es sensible a conocer el radio de AT del atacante.
- **[Demuestra]** No protege en CIFAR-100 con VGG-16 ni DenseNet-121 (Tabla 3). **[Inferencia]** El texto dice "generally more effective" sin comentar estas fallas.
- **[Demuestra]** Facescrub (10 identidades; Tabla 8): SEM 7–11 % frente a 50–69 % limpio.
- **[Demuestra]** Con filtros paso-bajo (ρ_u = 16/255, AT 2/255) SEM queda en 21.77–23.93 % en CIFAR-10 (Tabla 4).
- **[Demuestra]** Protección parcial con AT 2/255 (Tabla 11): SEM 20/40/60/80 % → 91.94 / 90.89 / 88.90 / 82.98 %. Solo con 100 % baja a 11.32 %. Los autores lo declaran como limitación.
- **[Demuestra]** Si REM usa otros ataques internos, cambia mucho (Tabla 14): PGD 48.16, FGSM 40.95, TPGD 33.19, APGD 80.87 %, frente a SEM 29.53 %.

#### P16.7 Caso aplicado
*Un fotógrafo publica 50 000 imágenes y sospecha que un recolector usará AT con radio 4/255.*
- Si protege con EM, el AT recupera 88.62 % de exactitud.
- Con REM, 48.16 % (y 22.6 h de cómputo).
- Con SEM, 31.92 % en ~6 h (CIFAR-10, Tablas 2 y 15).

Pero si el recolector usa VGG-16 en un problema tipo CIFAR-100, SEM no protege (57.11 % ≈ limpio). Y si el fotógrafo eligió ρ_r = 3 pensando que el atacante usaría 3/255 cuando en realidad usa 4/255, la exactitud sube a 62.09 % (Tabla 6).

#### P16.8 Fortalezas y limitaciones
- **Fortalezas:**
  - diagnóstico experimental claro (Tabla 1) y explicación cuantitativa (correlaciones);
  - 3–4× más barato que REM;
  - varios datasets y arquitecturas, 3 semillas;
  - pruebas con filtros y *early stopping*.
- **Limitaciones declaradas:** protección parcial débil; requiere ρ_a ≤ ρ_u.
- **Limitaciones inferidas:**
  - **[Inferencia]** requiere conocer ρ_a;
  - **[Inferencia]** fallas en VGG-16 y DenseNet-121 en CIFAR-100;
  - **[No especificado]** sin evaluación contra ISS/JPEG/grayscale, difusión (P22) o detección (P21);
  - **[Inferencia]** en la Tabla 5 (*early stopping*) el texto ("S_es=1500 increases test acc by 4%") no coincide con las columnas.

#### P16.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | La robustez de REM proviene del sustituto; el ruido entrenado adversarialmente es inestable ("monótono"). |
| **Demuestra** | Tabla 1 (descomposición), correlaciones (Tabla 10), mejoras frente a REM (Tabla 2), 3.91× más rápido (Tabla 15). |
| **Inferencia** | Sensibilidad a conocer ρ_a; fallas no comentadas en CIFAR-100. |
| **No especificado** | Robustez frente a compresión, difusión o detección. |

#### P16.10 Relación con los demás papers
- **Mejora directa de REM**, que es baseline común en P15 (como RUE), P22, P24, P25 y P26.
- **P24 (WDS)** usa las mismas cifras de SEM (13.00 / 31.92 % en CIFAR-10) y lo supera bajo AT con 15.27 %. **KBS** (Informe 1, P9) también usa SEM como baseline con las mismas cifras.
- La idea de "el ruido aleatorio estabiliza" contrasta con **ARMOR (P25)**, que entrena el ruido contra la **augmentation más alineada** (elegida por gradientes), y con **Segue (P15)**, que usa una capa de distorsión con AT pequeño.
- **[Inferencia]** La condición ρ_a ≤ ρ_u coincide con la teoría de presupuestos de P5 (Informe 1).

---

### P17 · T2UE (Text-to-Unlearnable)

**Título:** *T2UE: Generating Unlearnable Examples from Text Descriptions*. **Autores:** Xingjun Ma, Hanxun Huang (Univ. of Melbourne), Tianwei Song, Ye Sun, Yifeng Gao, Yu-Gang Jiang (Fudan). **Rol:** protección multimodal "zero-contact".

#### P17.1 Problema investigado
Los UEs (incluido **MEM**, la protección multimodal de pares imagen–texto) necesitan **la imagen original** para optimizar el ruido. Generar el ruido es costoso, así que el usuario común depende de un **servicio de terceros** al que debe entregar la imagen. Los autores lo llaman **"privacy paradox"**: para proteger la imagen hay que exponerla. Además, **[Afirma]** MEM depende de *triggers* textuales frágiles.

#### P17.2 Propuesta
**T2UE**: generar el ruido unlearnable **solo a partir de una descripción de texto** de la imagen, sin acceder a la imagen. El usuario recibe el ruido y lo suma localmente.

🔎 **Analogía.** Pides a un cerrajero que fabrique un candado para una caja que no le muestras; solo le dices "es una caja de madera de 30 cm con bisagras doradas". Con eso, él fabrica un candado que encaja razonablemente bien.

✅ **Explicación técnica correcta.** Un generador G recibe el **embedding de texto de CLIP** (congelado) y un ruido aleatorio z. Produce δ con ‖δ‖∞ ≤ 8/255 (Eq. 1) y se entrena para que, sumado a imágenes que corresponden a esa descripción, **minimice** la pérdida contrastiva de CLIP entre imagen perturbada y texto (Eq. 4). Así el ruido se alinea con el texto, como atajo, sin conocer la imagen concreta.

#### P17.3 Funcionamiento técnico
- **Generador:** arquitectura de **SSA-GAN** con bloques SSACN y **SSCBN** (*Semantic-Space Conditional BatchNorm*): los parámetros de normalización γ y β se calculan a partir del embedding de texto (Eq. 2).
- **Objetivo (Eq. 4):** minimizar InfoNCE de un CLIP sustituto congelado entre f_I(I+δ) y f_T(T). Es **error-minimizing en el espacio multimodal**.
- **Uso:**
  - el generador se entrena una vez en MSCOCO;
  - el usuario solo aporta texto + z y suma el ruido a su imagen;
  - *class-wise*: plantilla de texto fija + z fijo; *sample-wise*: plantillas o z variados.
- **Entrenamiento:**
  - CLIP ViT-B/32 (500 épocas) o ViT-B/16 (300 épocas), batch 128, Adam lr 1e-4 con *cosine*;
  - modelo objetivo supervisado: 100 épocas, SGD lr 0.1.

#### P17.4 Datos y resultado
- **Entrada:** texto descriptivo (y z). No requiere la imagen.
- **Salida:** δ para sumar a la imagen.
- **Requiere** un generador ya entrenado con un dataset imagen–texto (MSCOCO).

#### P17.5 Evaluación
- **CLIP RN50 entrenado desde cero** en Flickr8k, Flickr30k y Tiny-MSCOCO, con métricas de recuperación:
  - **Hit@10**: fracción de consultas cuyo par correcto aparece entre los 10 primeros (menor = mejor protección);
  - **Medr**: rango mediano del par correcto (mayor = mejor protección).
- **Clasificación supervisada:** CIFAR-10/100 (ResNet-18 y otras arquitecturas).
- **Pruebas adicionales:** protección parcial, augmentations, textos variados y tiempo.

#### P17.6 Resultados clave e interpretación

**Tabla P17-a (CLIP, Hit@10 I→T / T→I, %; P17 Tabla 2).**

| Dataset | Sin protección | EM (accede a imagen) | TAP | MEM-5 | **T2UE** |
|---|---|---|---|---|---|
| Flickr8k | 17.7 / 18.6 | 7.9 / 8.9 | 14.3 / 12.8 | 17.6 / 17.7 | **11.6 / 8.02** |
| Flickr30k | 49.3 / 44.1 | 5.5 / 6.1 | 39.7 / 37.2 | 47.1 / 43.3 | **18.2 / 15.62** |
| Tiny-MSCOCO | 33.3 / 15.5 | 3.7 / 3.9 | 7.4 / 6.0 | 29.2 / 15.4 | **7.4 / 5.14** |

**Tabla P17-b (clasificación ResNet-18, exactitud % class-wise / sample-wise; P17 Tabla 3).**

| Método | CIFAR-10 | CIFAR-100 |
|---|---|---|
| Sin protección | 90.34 | 65.98 |
| EM | 11.57 / 15.37 | 1.07 / 3.21 |
| UC | 79.41 / – | 41.38 / – |
| CP | 10.12 / 83.15 | 1.31 / 49.91 |
| **T2UE (ViT-B/16)** | **10.02 / 13.76** | **1.08 / 1.82** |

**Interpretación.**
- **[Demuestra]** T2UE reduce fuertemente la recuperación de CLIP sin ver la imagen. En Flickr30k la Medr I→T pasa de 11 a 78 (Tabla 2).
- **[Demuestra]** **EM, que sí accede a las imágenes, protege más** que T2UE en los tres datasets. Los autores lo reconocen como limitación ("aún no supera" a los métodos dependientes de la imagen).
- **[Demuestra]** **MEM evaluado solo con ruido de imagen** (sin sus *triggers* textuales) apenas protege (47.1 vs 49.3 en Flickr30k). **[Inferencia]** Esto es coherente con el argumento de que MEM depende del trigger, y con CMCR (Informe 1, P3), que rompe MEM atacando precisamente el texto.
- **[Demuestra]** Transferencia de arquitecturas (Tabla 4): 9.44–11.93 % class-wise en CIFAR-10, salvo sample-wise en VGG11 (28.43 %).
- **[Demuestra]** Protección parcial (Tabla 5, CIFAR-10 class-wise): 20 % → 79.46; 40 % → 78.01; 60 % → 74.46; 80 % → 68.44; 100 % → 10.02 %. **[Inferencia]** Con 80 % la exactitud baja más que en otros papers (≈ −22 puntos vs el limpio 90.34), pero no se reporta el control "entrenar solo con la parte limpia", así que no se sabe si los datos protegidos dañan o solo dejan de aportar.
- **[Demuestra]** Augmentations (ResNet-50; Tabla 6): con CutOut/MixUp/AutoAug se mantiene en 9.99–14.35 % en CIFAR-10.
- **[Demuestra]** Tiempo en Flickr8k: T2UE 0.3 h frente a EM 1.1 h, MEM-3 4.4 h, MEM-5 10.2 h y TAP 4.1 h (Tabla 8). **[Inferencia]** Este tiempo excluye el entrenamiento del generador (300–500 épocas sobre MSCOCO).
- **[Inferencia]** La Tabla 7 ("robusto a variaciones de texto", captions generadas con GIT) muestra en Flickr8K casi **ninguna** diferencia: Hit@10 I→T limpio 5.4 vs T2UE 5.6. Además, el "limpio" de esta tabla (5.4) es muy distinto del de la Tabla 2 (17.7), sin explicación del setup. La evidencia de esa afirmación es débil.

#### P17.7 Caso aplicado
*Una persona quiere subir a una red social 200 fotos de su familia con descripciones, sin que se usen para entrenar un modelo tipo CLIP, y no quiere enviar sus fotos a ningún servicio externo.*
- Con T2UE envía solo los textos ("una niña jugando en la playa al atardecer"), recibe los ruidos y los suma en su teléfono.
- Según la Tabla 2, un CLIP entrenado con esos pares bajaría su Hit@10 I→T en Flickr30k de 49.3 a 18.2.

**Matiz doble:** el texto mismo viaja al servicio (puede ser sensible) y la protección es menor que si se usara EM con acceso a las imágenes (5.5).

#### P17.8 Fortalezas y limitaciones
- **Fortalezas:**
  - plantea y resuelve el "privacy paradox";
  - funciona en multimodal y en supervisado;
  - muy rápido en uso;
  - robusto a augmentations comunes.
- **Limitaciones declaradas:** sensibilidad a la calidad del texto; protección inferior a métodos con acceso a la imagen.
- **Limitaciones inferidas:**
  - **[No especificado]** sin evaluación contra AT, JPEG/ISS, purificación por difusión (P22, CMCR) ni detección;
  - **[Inferencia]** ruido independiente de la imagen: textos iguales dan perturbaciones iguales, lo que podría facilitar estimarlas;
  - **[Inferencia]** inconsistencias de descripción: la Tabla 3 dice sustituto ResNet-18 y las Tablas 4–5 mencionan ResNet-50; el texto habla de "1 A100 for 32 epochs" y luego de 300–500 épocas.

#### P17.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Resuelve el "privacy paradox"; robusto a variaciones de texto; MEM depende de triggers frágiles. |
| **Demuestra** | Fuerte reducción de recuperación sin acceso a la imagen (Tabla 2); 10.02 % en CIFAR-10 (Tabla 3); robustez a augmentations (Tabla 6); 0.3 h (Tabla 8). |
| **Inferencia** | EM sigue siendo más fuerte; evidencia débil sobre variaciones de texto; parcial sin control. |
| **No especificado** | AT, compresión, purificación; costo del generador; venue. |

#### P17.10 Relación con los demás papers
- Comparte coautor (Xingjun Ma) y la idea de usar **CLIP** con **UC (P20)**; el md de P17 reporta UC como baseline débil en CIFAR-10 supervisado (79.41 %). **[Inferencia]** Es un setting distinto del que UC fue diseñado (label-agnostic, alta resolución), así que no contradice a P20.
- **Generador condicionado** como Segue (P15), TUE (P18) y GUE (Informe 1, P8).
- Frente a **CMCR** (Informe 1, P3), que rompe MEM purificando la imagen (con JCDP, el método de P22) y regenerando el texto, T2UE no evalúa su robustez. **[Inferencia]** Es la amenaza más directa a evaluar.

---

### P18 · TUE temporal (Video tracking)

**Título:** *Temporal Unlearnable Examples: Preventing Personal Video Data from Unauthorized Exploitation by Object Tracking*. **Autores:** Qiangqiang Wu, Yi Yu, Chenqi Kong, Ziquan Liu, Jia Wan, Haoliang Li, Alex C. Kot, Antoni B. Chan (CityU HK, NTU, QMUL, HIT Shenzhen). **Rol:** protección de video.

> **Aviso de sigla.** En este paper "TUE" significa *Temporal Unlearnable Examples*. En P15 y en otros papers "TUE" es *Transferable Unlearnable Examples* (Ren et al.), un método distinto.

#### P18.1 Problema investigado
Videos personales (p. ej., de YouTube) se usan para entrenar **trackers** de objetos (datasets como GOT-10k, LaSOT, TrackingNet). Los UEs de imagen aplicados a video son:
- **ineficientes**: alta resolución, muchos *frames*;
- **poco efectivos**: el tracking empareja una plantilla con regiones de búsqueda a distintas escalas;
- **poco generalizables**: entre trackers, datasets y tareas.

**Visual Object Tracking (VOT)** — 🔎 *Analogía:* seguir con el dedo a un perro concreto en un video, sabiendo solo cómo se veía en el primer cuadro. ✅ *Técnico:* el tracker recibe una **plantilla** z (recorte del objeto) y una **región de búsqueda** x; en **SiamFC** calcula la correlación cruzada f(z) * f(x) y se entrena con BCE contra un mapa de respuesta y (Eq. 2). Métricas:
- **AO** (*average overlap*);
- **SR₀.₅ / SR₀.₇₅** (tasa de éxito con solapamiento > 0.5 / 0.75);
- **AUC** del gráfico de éxito y **precisión**.

#### P18.2 Propuesta
1. **Baseline propio:** adaptar EM a tracking (Eq. 3), con un ruido por video interpolado al tamaño de la bbox y pegado en plantilla y búsqueda, más un ruido de contexto. Es lento, de forma fija y específico por video.
2. **TUE generativo (Eq. 4):** un generador G_w(z, b̃) basado en **DiT-S/8** (*Diffusion Transformer*: 12 capas, 6 cabezas, dimensión 384), adaptado a **un solo paso feed-forward** y condicionado por el estado normalizado del objetivo b̃. Produce un ruido consciente del objetivo que se pega en la plantilla y en el recorte del objeto en la búsqueda.
3. **Temporal Contrastive Loss (Eq. 5):** L + λL_cl(f(ẑ), f(ê)) con λ = 0.05.
   - Positivo: la versión TUE del mismo objetivo en otro *frame* del mismo video.
   - Negativos: plantillas limpias del mismo video y de otros videos.
   - El generador (nivel interior) usa L + λL_cl; el tracker (nivel exterior), solo L.

🔎 **Analogía.** En lugar de pintar a mano una marca distinta en cada cuadro del video, usas un sello automático que, al ver dónde está el perro en cada cuadro, estampa una marca coherente a lo largo del video, para que el "seguidor" aprenda a seguir la marca y no al perro.

✅ **Explicación técnica correcta.**
- El "sello" es el generador condicionado por la bbox.
- La "coherencia" la impone la pérdida contrastiva: la representación del objetivo perturbado debe parecerse a la de otras apariciones perturbadas y alejarse de las limpias.
- El tracker minimiza su pérdida de matching usando la perturbación como atajo (Fig. 3: menor pérdida de entrenamiento; Fig. 7: la atención se concentra en la perturbación).

#### P18.3 Funcionamiento técnico
- **Entrenamiento del generador:** sustituto SiamFC en GOT-10k, 50 épocas, lr 5e-6, batch 16, 7 h en una RTX 4090; optimización alterna con Adam (Alg. 1).
- **Pipeline (Fig. 4):** se requieren las *bounding boxes* del objeto. El usuario puede anotarlas o usar una propuesta no supervisada (EdgeBox; resultados en el material suplementario, no incluido).

#### P18.4 Datos y resultado
- **Entrada:** video + bbox del objeto en cada *frame*; generador entrenado.
- **Salida:** video con ruido en la región del objeto.

#### P18.5 Evaluación
- **Trackers (8 en la Tabla 4):** SiamFC, OSTrack, DropTrack, SeqTrack, MixFormer, STARK, AQATrack y HIPTrack.
- **Datasets:** GOT-10k, OTB y LaSOT; también segmentación de objetos en video (VOS: DAVIS-17 y YouTube-VOS con STCN y XMem).
- **Baselines:** TAP, LSP, AR, EM.

#### P18.6 Resultados clave e interpretación

**Tabla P18-a (ablación; OTB AUC / GOT-10k AO; P18 Tabla 1).**

| Variante | SiamFC OTB AUC | SiamFC GOT AO | OSTrack OTB AUC | OSTrack GOT AO |
|---|---|---|---|---|
| Limpio | 58.6 | 35.5 | 67.4 | 71.0 |
| EM baseline | 39.6 | 27.0 | – | – |
| EM + Context | 29.5 | 21.4 | – | – |
| TUE Generator | 17.6 | 16.1 | 45.3 | 41.6 |
| **+ TCL (TUE completo)** | **11.4** | **12.1** | **30.5** | **18.0** |

**Tabla P18-b (transferencia entre trackers; GOT-10k AO; P18 Tabla 4).**

| Tracker | Limpio | TAP | LSP | AR | EM | **TUE** |
|---|---|---|---|---|---|---|
| SiamFC | 35.5 | 32.9 | 28.1 | 34.1 | 21.4 | **12.1** |
| OSTrack-256 | 71.0 | **17.0** | 27.8 | 67.0 | 26.3 | 18.0 |
| DropTrack-384 | 75.9 | 51.7 | 27.8 | 66.9 | 21.6 | **17.1** |
| SeqTrack-256 | 74.7 | 8.1 | 13.5 | 64.3 | 8.1 | **2.1** |
| MixFormer-CvT | 70.7 | 12.4 | 20.9 | 51.4 | 7.0 | **1.9** |
| STARK-S50 | 67.2 | 18.6 | 8.7 | 51.7 | 14.8 | **2.6** |
| AQATrack-256 | 73.2 | – | – | – | 19.4 | **16.2** |
| HIPTrack | 77.4 | – | – | – | 63.0 | **43.2** |

**Interpretación.**
- **[Demuestra]** Cada componente ayuda (Tabla 1). La pérdida temporal contrastiva es crucial para trackers modernos: en OSTrack el AO baja de 41.6 a 18.0.
- **[Demuestra]** Eficiencia (Tabla 2): EM + Context requiere 33 h y 3.4 GB de parámetros; el generador TUE, 7 h y 124 MB.
- **[Demuestra]** TUE es el mejor en la mayoría de trackers. **[Inferencia]** No en todos:
  - en OSTrack-256, TAP (17.0) supera a TUE (18.0) en GOT-10k AO;
  - en STARK-S50, TAP es mejor en OTB (18.7 vs 13.9 de TUE);
  - en HIPTrack la protección es débil (77.4 → 43.2). El paper indica que HIPTrack usa DropTrack congelado como base.
- **[Demuestra]** Dataset no visto (Tabla 5, STARK-S50 entrenado en LaSOT): GOT AO 55.6 → 4.1; LaSOT AUC 58.2 → 5.7.
- **[Demuestra]** Más épocas no rompen la protección (Tabla 3, SeqTrack): 9.7 / 2.1 / 2.3 AO con 100 / 200 / 500 épocas, frente a 74.7 limpio.
- **[Demuestra]** En VOS el efecto es menor (Tabla 6):
  - XMem: J&F 84.5 → 64.2 en DAVIS-17 y 84.2 → 61.3 en YTVOS;
  - STCN: 82.5 → 63.6 y 82.7 → 65.7.

  **[Inferencia]** La degradación es de ~17–23 puntos, mucho menor que en VOT.
- **[Demuestra]** Entrenar el generador con más videos mejora la protección (Fig. 5): AUC ~59 con 0 % de videos, ~20 con 25 %, ~16 con 50 % y ~11 con 100 %.

#### P18.7 Caso aplicado
*Un creador de contenido sube a su canal cientos de videos de su mascota y no quiere que se usen para entrenar trackers comerciales.*
- Anota (o detecta automáticamente) la caja de la mascota y aplica el generador TUE.
- Si una empresa entrena SeqTrack con esos videos, su AO en GOT-10k bajaría de 74.7 a 2.1 (Tabla 4).
- Si entrena HIPTrack (que parte de un modelo preentrenado congelado), solo bajaría a 43.2.

**Lectura práctica:** la protección es más débil cuando el atacante reutiliza un modelo preentrenado en datos limpios.

#### P18.8 Fortalezas y limitaciones
- **Fortalezas:**
  - primer UE para tracking;
  - generador eficiente;
  - evaluación amplia (8 trackers, 3 benchmarks de VOT, 2 de VOS, dataset no visto);
  - análisis de atención.
- **Limitaciones inferidas:**
  - **[No especificado]** presupuesto ε / imperceptibilidad (solo "imperceptible", visualizaciones en el suplementario);
  - **[No especificado]** ninguna defensa evaluada: AT, augmentations, compresión de video, purificación, detección;
  - **[No especificado]** protección parcial: videos protegidos mezclados con limpios;
  - **[Inferencia]** dependencia de las bboxes;
  - **[Inferencia]** menor efecto con modelos que reutilizan *backbones* congelados (HIPTrack).

#### P18.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | 4× más rápido y 28× menos parámetros; generaliza entre trackers, datasets y tareas. |
| **Demuestra** | Tablas 1–6: fuertes caídas de AO/AUC en 8 trackers; dataset no visto; VOS. |
| **Inferencia** | No siempre es el mejor (TAP en OSTrack); débil en HIPTrack; efecto menor en VOS. |
| **No especificado** | ε, defensas, protección parcial, métricas visuales. |

#### P18.10 Relación con los demás papers
- Es el único paper (de ambos informes) en **video**.
- Comparte la **pérdida contrastiva** como herramienta con T2UE (P17, InfoNCE para generar) y TPD (P14, InfoNCE para defender).
- Comparte el **generador condicionado** con P15, P17 y P20.
- Comparte autores (Yi Yu, Alex Kot; NTU) con **P21** (Iterative Filtering). **[Inferencia]** P21 sugiere que estas perturbaciones, por ser atajos rápidos de aprender, podrían ser detectables en un dataset mixto; P18 no lo evalúa.

---

### P19 · Bayes Error Optimization

**Título:** *Towards Provably Unlearnable Examples via Bayes Error Optimization*. **Autores:** Ruihan Zhang, Jun Sun, Ee-Peng Lim, Peixin Zhang (Singapore Management University). **Publicación:** AAAI 2026. **Rol:** protección con garantía teórica.

#### P19.1 Problema investigado
Dos problemas de los UEs existentes:
1. **Solo funcionan si todo el training set es UE.** **[Demuestra]** CIFAR-10 (§1 y RQ2):

   | Datos de entrenamiento | Exactitud |
   |---|---|
   | UE (EM) solo | 20.50 % |
   | Mitad limpia + mitad UE | 92.51 % |
   | Solo la mitad limpia | 91.16 % |

   Es decir, los UEs mezclados **no reducen** e incluso ayudan ligeramente.
2. **Carecen de garantías formales:** son heurísticas.

#### P19.2 Propuesta
Construir UEs que **maximicen el Bayes error** del dataset.

**Bayes error** — 🔎 *Analogía:* si dos gemelos idénticos usan la misma ropa, ni el mejor detective del mundo puede distinguirlos en una foto borrosa; esa tasa de error "inevitable" es el Bayes error. ✅ *Técnico:* β = E_x[1 − max_c p(y=c | x)] (Eq. 4): el error mínimo que puede lograr **cualquier** clasificador dada la distribución de los datos. Aumentarlo implica que ningún modelo puede aprender bien, sin depender de un atajo.

🔎 **Analogía adicional.** En lugar de dejar una "pista falsa" (atajo), haces que las fotos de perros y gatos se parezcan entre sí lo suficiente para que nadie pueda separarlas bien.

✅ **Explicación técnica correcta.** Se perturba cada x (‖x' − x‖ₚ ≤ ε) en la dirección que **aumenta** un estimador del Bayes error calculado con vecinos. En la práctica, esto acerca ejemplos de distintas clases en el espacio de representaciones.

#### P19.3 Funcionamiento técnico
- **Estimador (Alg. 1, Eq. 6–7):** promedio local del posterior tipo kernel. p̂(y = c | xᵢ) es la suma de similitudes gaussianas s(xⱼ, xᵢ) de los vecinos de clase c dividida por la suma total (excluyendo i), y β̂ = 1 − (1/n) Σ max_c p̂.
  - **Ejemplo 1:** con dos normales truncadas, Bayes error analítico 0.1427, estimado 0.1426.
- **Cotas (Lemma 3.1, Teorema 3.2):** |β̂ − β| ≤ 2C₂|Y|σ² + 2C₁|Y|/√(nσ^d). El error del estimador se controla con el ancho de banda σ y el número de muestras n (pruebas en el suplementario).
- **Optimización (Eq. 13, Alg. 2):** *Projected Gradient Ascent* (PGA) sobre x' para maximizar β̂.
  - **Lemma 3.3:** PGA con gradiente κ-Lipschitz y paso η ∈ (0, 2/κ) no disminuye el objetivo.
  - **Teorema 3.4:** existe κ tal que β̂ del dataset perturbado ≥ β̂ del original ("provably").
  - **Corolario 3.5:** si algunas perturbaciones se fijan en 0 (mezcla con datos limpios), el resto aún aumenta β̂.
- **En imágenes:** β̂ se calcula en el **espacio de embeddings de un ResNet-18** (Eq. 19), no en píxeles.

#### P19.4 Datos y resultado
- **Entrada:** dataset etiquetado (o la parte que el protector controla) y un extractor ResNet-18.
- **Salida:** imágenes perturbadas (L∞ ε = 8/255).

#### P19.5 Evaluación
- **Datos:** Moons (sintético), CIFAR-10, CIFAR-100 y Tiny ImageNet.
- **Entrenamiento de la víctima:** 100 épocas, SGD lr 0.1.
- **Comparación** en mezclas con EM (Huang) y EntF (Wen); arquitecturas varias; AT con PGD.

#### P19.6 Resultados clave e interpretación

**Tabla P19-a (CIFAR-10).**

| Escenario | Exactitud % | Fuente |
|---|---|---|
| Limpio completo | 95.12 | RQ2 |
| Todo protegido con Bayes-UE | 28.12 | RQ2 |
| Solo la mitad limpia | 91.16 | §1 / RQ2 |
| Mitad limpia + mitad EM | 92.51 | §1 / RQ2 |
| **Mitad limpia + mitad Bayes-UE** | **69.68** | RQ2 |
| AT (PGD) sobre Bayes-UE | 74.5 | RQ3 / Fig. 6 |

**Tabla P19-b (arquitecturas, CIFAR-10, limpio / Bayes-UE; P19 Tabla 1).**

| Modelo | Limpio | Bayes-UE |
|---|---|---|
| ResNet-18 | 95.04 | 28.12 |
| ResNet-34 | 95.12 | 29.86 |
| VGG-19 | 91.70 | 18.14 |
| DenseNet-121 | 95.06 | 25.89 |
| MobileNet v2 | 93.30 | 30.54 |

**Interpretación.**
- **[Demuestra]** En mezclas, el método **sí** reduce la exactitud por debajo de entrenar solo con la parte limpia (69.68 vs 91.16).
  - EM y EntF no lo logran (Fig. 4).
  - **[Afirma]** En promedio la caída es 8 puntos mayor que con Huang (EM) y 9 mayor que con Wen (EntF).
- **[Demuestra]** El estimado del Bayes error aumenta:
  - Moons (ε = 0.25): 0.1434 → 0.1888 (+24.3 %);
  - CIFAR-10 (embeddings): 0.0565 → 0.2453 (> 3.3×).
- **[Inferencia]** Con todo el dataset protegido (28.12 %) es **menos fuerte** que EM (20.50 %, cifra citada en §1). El método sacrifica fuerza "pura" a cambio de funcionar en mezclas.
- **[Inferencia]** Lectura de la Fig. 4 (valores aproximados del md):

  | % de datos originales (limpios) | Sin add-on | Con Bayes-UE |
  |---|---|---|
  | 50 % | ~91 | ~70 |
  | 90 % | ~91 | ~85.5 |
  | 99.99 % | ~95 | ~93.5 |

  Con 99.99 % de datos limpios (≈5 imágenes protegidas de 50 000) todavía hay una caída de ~1.5 puntos, y la curva "No add-on" no es monótona (94 → 91 → 95). Sin varianza reportada, parte de estas diferencias podría ser ruido entre corridas.
- **[Inferencia]** La garantía "provable" es sobre el **estimado** β̂ (no disminuye, desigualdad ≥) bajo supuestos de suavidad que el propio paper relaja (el objetivo es diferenciable "casi en todas partes"). No es una cota sobre la exactitud de una red concreta.
- **[Demuestra]** Con AT la exactitud sube a 74.5 % (21.5 puntos menos que limpio, según el texto). **[No especificado]** El radio de AT.

#### P19.7 Caso aplicado
*Un hospital publica 25 000 radiografías anonimizadas para investigación abierta, pero sabe que un tercero las combinará con 25 000 radiografías limpias propias para entrenar un producto comercial.*
- Con EM, el tercero no notaría nada: en CIFAR-10, la mezcla mitad/mitad da 92.51 %, incluso más que solo la mitad limpia.
- Con la protección por Bayes error, el modelo combinado bajaría a ~69.68 % (análogo CIFAR-10, RQ2).

**Matiz:** si el tercero hace AT, recupera hasta ~74.5 %. (El escenario es ilustrativo; el paper solo evalúa CIFAR, Tiny ImageNet y Moons.)

#### P19.8 Fortalezas y limitaciones
- **Fortalezas:**
  - ataca explícitamente la protección parcial, el problema que el Informe 1 identificó como talón de Aquiles;
  - base teórica (estimador con cotas, no-disminución);
  - transfiere a varias arquitecturas.
- **Limitaciones inferidas:**
  - **[No especificado]** radio de AT, varianza, costo computacional (las similitudes son O(n²) para n = 50k) y valor de σ;
  - **[Inferencia]** solo dos baselines en la mezcla;
  - **[Inferencia]** sin pruebas contra ISS, augmentations, purificación o detección;
  - **[Inferencia]** más débil que EM con todo protegido;
  - **[Inferencia]** la garantía es sobre el estimador en el espacio de embeddings de un extractor concreto.

#### P19.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | "Provably" unlearnable; 8–9 puntos más de caída que EM/EntF en mezclas. |
| **Demuestra** | Teorema 3.4 (no-disminución de β̂); 69.68 % en mezcla 50/50; 28.12 % con todo protegido; transferencia (Tabla 1). |
| **Inferencia** | Garantía limitada al estimador; trade-off fuerza vs mezcla; ruido en la Fig. 4. |
| **No especificado** | Radio AT, σ, costo, varianza. |

#### P19.10 Relación con los demás papers
- Es el **único** del bloque cuyo mecanismo **no es un atajo** sino "mezclar las clases". **[Inferencia]** En espíritu se parece más a los métodos error-maximizing/EntF que a EM.
- Responde al hallazgo de protección parcial del Informe 1 (P2, P9, P10) y de este bloque (P16 Tabla 11, P20 Fig. 6a, P24 Tabla 3, P25 Tablas XV–XVI).
- Su enfoque teórico se parece al de GUE (Informe 1, P8: existencia de equilibrio) y P5 (Informe 1: separabilidad), pero con un objetivo diferente.
- **[Inferencia]** Al no basarse en un atajo, podría ser **menos detectable** por métodos como P21 (que explota la rapidez de aprendizaje de los atajos). No está evaluado.

---

### P20 · Unlearnable Clusters (UC / UC-CLIP)

**Título:** *Unlearnable Clusters: Towards Label-agnostic Unlearnable Examples*. **Autores:** Jiaming Zhang, Xingjun Ma, Qi Yi, Jitao Sang, Yu-Gang Jiang, Yaowei Wang, Changsheng Xu (Beijing Jiaotong, Fudan, CAS, Peng Cheng Lab). **Publicación:** no visible en el md (P15 lo cita como CVPR 2023). **Rol:** protección sin depender de las etiquetas.

#### P20.1 Problema investigado
Los UEs suponen **label-consistency**: protector y atacante usan las mismas etiquetas. En la práctica, el atacante puede **re-etiquetar** el dataset con otra granularidad (p. ej., "gato/perro" → "animal" o 37 razas → 3 grupos). Los autores llaman **label-agnostic** a este escenario.

**[Demuestra]** Si el protector genera UEs con las etiquetas predichas por un sustituto de ImageNet y la víctima entrena con las etiquetas de CIFAR-10 (ResNet-18; Fig. 2a), los UEs existentes colapsan:

| Método | Label-consistency | Label-agnostic |
|---|---|---|
| EMinN | 19.93 % | 93.77 % |
| AdvPoison | 6.25 % | 93.6 % |
| SynPer | 13.54 % | 93 % |
| DeepConfuse | 28.77 % | 93.31 % |

**[Afirma]** Los UEs existentes "sobreajustan a las etiquetas".

#### P20.2 Propuesta
**UC (Unlearnable Clusters)**: generar perturbaciones **por cluster** (no por clase), encontradas sin etiquetas, que alteren la estructura de representaciones de forma independiente de cómo se etiqueten los datos. **UC-CLIP** usa el *image encoder* de **CLIP** como sustituto.

🔎 **Analogía.** En vez de poner una pista falsa por *materia* (que deja de servir si el copión reorganiza sus apuntes por *tema*), agrupas las páginas que "se parecen" y desplazas cada grupo para que parezca otro grupo; reorganices como reorganices, el contenido queda revuelto.

✅ **Explicación técnica correcta.** K-means sobre las *features* de un sustituto encuentra p clusters (p = 10 por defecto). Para cada cluster Cᵢ se entrena un generador que produce una perturbación (la misma para todo el cluster, acotada por ε) que **empuja todas sus imágenes hacia el centro de otro cluster**.

#### P20.3 Funcionamiento técnico
- **Análisis motivador (Fig. 2b, PCA 3D):** los UEs de EMinN y AdvPoison **reducen la varianza** y **colapsan** clases, rompiendo la "discrepancy" (diversidad intra-clase), y sacan puntos del manifold, rompiendo la "uniformity". Es una interpretación cualitativa.
- **Algoritmo (Alg. 1):**
  1. Embeddings E = f_s(X) con el sustituto (ResNet-50 de ImageNet o CLIP).
  2. K-means → p clusters con centros μ.
  3. Para cada Cᵢ, un generador encoder–decoder (reinicializado) toma ruido uniforme σ y produce δᵢ (clamp ±ε). Se minimiza la pérdida **DDU** (*Disrupting Discrepancy and Uniformity*): L_DDU = Σⱼ d(f_s(x_ij + G(σ; θᵢ)), g(μ_{Cᵢ})), donde g es una permutación de centros (i → i+1).
- **Presupuesto:** ε = 16/255 (L∞), el doble del estándar.
- **Entrenamiento del generador:** 10 épocas por cluster (ImageNet*) o 50 (resto).

#### P20.4 Datos y resultado
- **Entrada:** imágenes **sin etiquetas** y un sustituto.
- **Salida:** imágenes con perturbación cluster-wise.

#### P20.5 Evaluación
- **Datasets de alta resolución:** Pets, Cars, Flowers, Food, SUN397, ImageNet* (100 primeras clases).
- **Víctimas desde cero** con augmentations: ResNet-18, EfficientNet-B1, RegNetX-1.6GF.
- **Baselines** evaluados en el setting label-agnostic: DeepConfuse, SynPer, EMinN, EMaxN, AdvPoison.
- **Otras pruebas:** re-etiquetado con distinto número de clases; SimCLR; servicios comerciales (Azure, PaddlePaddle); defensas (Mixup, suavizado gaussiano, CutMix, Cutout); número de clusters; mezclas.

#### P20.6 Resultados clave e interpretación

**Tabla P20-a (ResNet-18, exactitud %; P20 Tabla 1).**

| Método | Pets | Cars | Flowers | Food | SUN397 | ImageNet* |
|---|---|---|---|---|---|---|
| Limpio | 62.31 | 67.18 | 67.18 | 78.97 | 43.08 | 77.76 |
| Mejor baseline | 50.86 | 51.11 | 50.58 | 73.13 | 34.41 | 55.12 |
| UC | 12.21 | 33.57 | 35.55 | 55.29 | 20.38 | 54.80 |
| **UC-CLIP** | **4.69** | **4.74** | **10.07** | **19.07** | **3.89** | **39.78** |

**Tabla P20-b (servicios comerciales, Cars; P20 Tabla 2).**

| Método | Azure | PaddlePaddle |
|---|---|---|
| Limpio | 48.45 | 83.74 |
| Mejor baseline (DeepConfuse) | 39.47 | 41.88 |
| UC (RN50) | 36.40 | 30.96 |
| **UC-CLIP (ViT-B/32)** | **22.47** | **11.49** |

**Interpretación.**
- **[Demuestra]** UC-CLIP es el más fuerte en casi todos los casos; UC con ResNet-50 es intermedio. **[Inferencia]** Excepciones:
  - con EfficientNet-B1 en Cars, UC (13.92) supera a UC-CLIP (15.33);
  - con RegNetX en ImageNet*, DeepConfuse (49.88) supera a UC (56.10).
- **[Demuestra]** Con re-etiquetado de Pets (BERT + K-means, 3 a 37 clases; Fig. 4a), UC y UC-CLIP quedan cerca del azar.
- **[Demuestra]** Con SimCLR en Pets (Fig. 4b): UC 17.83 %, UC-CLIP 26.11 %, DeepConfuse 24.37 %, frente a 48.3 % limpio.
- **[Demuestra]** Defensas (Tabla 3, Pets ResNet-18): UC 12.21–24.26 %; UC-CLIP 4.69–18.59 %. La peor es el suavizado gaussiano.
- **[Demuestra]** Más clusters → más protección (Fig. 5, UC): p = 5 → 16.08; p = 15 → 4.42; p = 30 → 2.75.
- **[Demuestra]** **Mezclas (Fig. 6a):** entrenar con algunas clases limpias y el resto UC da casi lo mismo que entrenar solo con las clases limpias. Las clases protegidas no aportan; **solo quien adopta la protección queda protegido**.
- **[Demuestra]** La Fig. 6b muestra que el modelo aprende la distribución del ruido: test con ruido UC >60 % (≈ limpio) y test limpio ~10 %.

#### P20.7 Caso aplicado
*Un banco de imágenes de mascotas protege su catálogo con un UE clásico usando sus 37 razas como etiquetas. Un competidor descarga las fotos y entrena un clasificador de solo 3 categorías (perros grandes, perros pequeños, gatos).*
- Con un UE clásico, la protección colapsa (en CIFAR-10 la exactitud sube a ~93 % en el setting label-agnostic; Fig. 2a).
- Con UC-CLIP, Pets queda en 4.69 % con ResNet-18 (Tabla 1). El re-etiquetado no la rompe (Fig. 4a).

**Matiz:** usa ε = 16/255 (más visible) y no se evaluó contra AT.

#### P20.8 Fortalezas y limitaciones
- **Fortalezas:**
  - identifica un supuesto oculto (label-consistency) y lo rompe con evidencia;
  - método sin etiquetas;
  - pruebas con servicios comerciales, SimCLR y re-etiquetado.
- **Limitaciones inferidas:**
  - **[Inferencia]** ε = 16/255 sin cuantificar la perceptibilidad;
  - **[No especificado]** sin AT ni ISS/JPEG;
  - **[No especificado]** costo de entrenar p generadores;
  - **[Inferencia]** los baselines se evalúan en el setting donde por diseño fallan, lo que favorece la comparación;
  - **[Inferencia]** exactitudes limpias modestas (44–84 %), modelos entrenados desde cero;
  - **[Inferencia]** la detección (task-agnostic) queda como trabajo futuro.

#### P20.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Los UEs sobreajustan a las etiquetas; UC rompe "discrepancy" y "uniformity". |
| **Demuestra** | Colapso de UEs en label-agnostic (Fig. 2a); UC/UC-CLIP efectivos en 6 datasets, 3 arquitecturas, 2 servicios comerciales (Tablas 1–2); re-etiquetado (Fig. 4a). |
| **Inferencia** | Comparación favorable por diseño; ε grande; costo no reportado. |
| **No especificado** | AT, perceptibilidad, costo, venue. |

#### P20.10 Relación con los demás papers
- **P22 (LE)** rompe UC y UC-CLIP en Pets con SimCLR (17.9 → 46.0 % y 26.1 → 47.5 %, vs 48.3 % limpio; Tabla 4 de P22).
- **P17 (T2UE)** lo reporta débil en CIFAR-10 supervisado (79.41 %), un setting distinto.
- **Segue (P15)** usa la misma idea de K-means para pseudo-etiquetas.
- Comparte con **P19** la observación de que la mezcla con datos limpios anula el efecto, pero UC no intenta resolverla.

---

### P21 · Iterative Filtering (IF)

**Título:** *Unlearnable Examples Detection via Iterative Filtering*. **Autores:** Yi Yu, Qichen Zheng, Siyuan Yang, Wenhan Yang, Jun Liu, Shijian Lu, Yap-Peng Tan, Kwok-Yan Lam, Alex Kot (NTU, PengCheng Lab, Lancaster). **Rol:** detección.

#### P21.1 Problema investigado
Dado un dataset **mixto** D = D_u ∪ D_c (UEs + limpios), identificar qué muestras son UEs (Eq. 2: clasificación binaria por muestra). **[Afirma]** "IF is the first UEs detection method without any baselines" (§5.1), por lo que comparan con detectores de *backdoor*.

#### P21.2 Propuesta
**Iterative Filtering (IF)**: un procedimiento iterativo con clasificadores entrenados con *early stopping*, **clases adicionales** para los datos ya identificados como limpios y un paso final de **recuperación**.

🔎 **Analogía.** En un curso, los alumnos que copiaron (UEs) responden perfecto desde la primera semana, mientras los honestos (limpios) mejoran lentamente. Si examinas a mitad del curso a alumnos que no viste practicar, los copiones aciertan y los honestos todavía fallan; repitiendo esto con grupos al azar, separas a unos de otros.

✅ **Explicación técnica correcta.**
- **"UEs make faster learners" (§4.1, Fig. 1):** con un dataset 50/50, un clasificador entrenado con la mitad alcanza ~100 % en UEs no vistos en pocas épocas (EM, OPS), mientras en los limpios mejora lento. La razón es que los UEs del mismo método comparten distribución (atajo).
- **Iteración:** se divide al azar D en entrenamiento (p = 50 %) y validación; se entrena un clasificador de **2C clases** con *early stopping* y se re-etiqueta la validación.
- **Clases adicionales:** las muestras identificadas como limpias pasan a la etiqueta y + C (Fig. 2, Eq. de §4.1). Así siguen participando en el entrenamiento como "clase limpia" y ayudan a separar ambos grupos (t-SNE, Fig. 4).
- **Recuperación:** una sola vez, se entrena un clasificador de C clases con las muestras aún etiquetadas como UE y se devuelven a UE las muestras de [C, 2C−1] que ese clasificador acierta.
- **Parada:** cuando el número de actualizaciones N_update cae por debajo de N_thre.
- **Salida:** D̂_u = muestras con y ∈ [0, C−1]; D̂_c = muestras con y ∈ [C, 2C−1].

#### P21.3 Funcionamiento técnico (detalles y una ambigüedad)
- **Clasificador:** ResNet-18.
- **Hiperparámetros:** proporción p y umbral N_thre (Alg. 1).
- **Ambigüedad del pseudocódigo:**
  - **[No especificado / posible errata]** El md escribe la regla de actualización como yᵢ = yᵢ + C·I{(ŷᵢ = yᵢ) & (yᵢ ∈ [0, C−1])}, es decir, mueve a "limpio" (y + C) las muestras que el modelo **acierta**.
  - Según la intuición del §4.1, las muestras acertadas tempranamente son las **UEs**, y la salida del algoritmo devuelve como limpias las de [C, 2C−1].
  - **[Inferencia]** Hay una aparente contradicción entre la condición del pseudocódigo y la intuición; el md no permite resolverla. La descripción conceptual (§4.1) y los resultados no dependen de ese detalle de notación.

#### P21.4 Datos y resultado
- **Entrada:** dataset mixto etiquetado. No requiere datos limpios de confianza ni conocer el método de protección.
- **Salida:** partición en UEs detectados y limpios.

#### P21.5 Evaluación
- **Datasets:** CIFAR-10, CIFAR-100, ImageNet subset (100 clases).
- **UEs:** EM y REM (L∞ 8/255), AR, LSP (L2 = 1.0), OPS (L0 = 1).
- **Poison ratio:** 20, 40, 60 y 80 %.
- **Baselines** (detectores de backdoor): **TeCo** (consistencia de robustez a corrupciones en test) y **Deep k-NN** (vecinos en *features* profundas).
- **Métricas:** Acc, FAR, FRR, HTER (§1.13).
- **Ablación:** sin *early stopping* (ES), sin recuperación, sin clases adicionales (AC).
- **Aplicación:** DiffPure solo sobre lo detectado.

#### P21.6 Resultados clave e interpretación

**Tabla P21-a (CIFAR-10, HTER % de IF por poison ratio; P21 Tabla 1).**

| UE | 20 % | 40 % | 60 % | 80 % |
|---|---|---|---|---|
| EM | 11.4 | 7.9 | 4.0 | 1.5 |
| OPS | 7.1 | 5.4 | 5.2 | 8.3 |
| LSP | **40.0** | 13.7 | 6.4 | 5.1 |
| REM | **46.2** | 13.3 | 14.5 | 11.1 |
| AR | **49.0** | **41.5** | **36.9** | 21.7 |

**Promedios en CIFAR-10 (Acc / FAR / FRR / HTER):**

| Detector | Acc | FAR | FRR | HTER |
|---|---|---|---|---|
| **IF** | 84.5 | 17.3 | 17.8 | **17.5** |
| Deep k-NN | 56.1 | 34.5 | 42.4 | 38.5 |
| TeCo | 50.4 | 50.1 | 50.1 | 50.1 |

**Tabla P21-b (DiffPure y detección, CIFAR-10, exactitud % promedio sobre 5 UEs; P21 Tabla 5).**

| Poison ratio | Sin defensa | DiffPure en todo | **DiffPure solo en lo detectado** |
|---|---|---|---|
| 60 % | 91.78 | 85.64 | **92.17** |
| 80 % | 87.47 | 85.91 | **90.61** |

**Interpretación.**
- **[Demuestra]** IF es muy superior a los detectores de backdoor:
  - **TeCo** está en el nivel de azar (HTER ~50 %). Los autores lo explican porque el backdoor cambia la predicción hacia una clase objetivo y el UE no.
  - **Deep k-NN** falla con desbalance fuerte.
- **[Demuestra]** **Falla** con 20 % de veneno para LSP, REM y AR, y con AR también al 40–60 %. **[Afirma]** Explicación de los autores:
  - con 20 % el veneno es minoría y "un pequeño porcentaje de UEs no afecta significativamente el desempeño", así que la detección importa menos;
  - AR es autoregresivo, más complejo y no linealmente separable.
- **[Demuestra]** CIFAR-100 e ImageNet subset (Tablas 2–3; EM, OPS, LSP) con IF:
  - ImageNet subset, promedio: Acc 93.7 y HTER 7.9;
  - en CIFAR-100 con 20 % de LSP, HTER 49.8 (falla).
- **[Inferencia]** **Error en la tabla:** la fila "Average" de CIFAR-100 es idéntica a la de ImageNet (59.2/33.2/41.5/37.4 | 52.2/47.5/50.2/49.3 | 93.7/11.5/4.1/7.9). Los valores por fila de CIFAR-100 dan un promedio de IF de **Acc ≈ 89.7 y HTER ≈ 14.2** (cálculo propio); los de ImageNet sí dan 93.7 / 7.9.
- **[Demuestra]** Ablación (EM; Tabla 4 del paper, rotulada en el md como "Table 2"):

  | Variante | HTER promedio |
  |---|---|
  | IF completo | 4.5 |
  | Sin clases adicionales (AC) | **11.1** (el peor) |
  | Sin recuperación | 6.4 |
  | Sin *early stopping* | 4.8 |

  Sin *early stopping* el resultado es incluso mejor al 40 % (HTER 3.9 vs 7.9). Los autores dicen que ES "puede afectar ligeramente con poison ratio bajo" pero es más eficiente.
- **[Demuestra]** Detectar y purificar solo lo detectado supera a no hacer nada y a purificar todo.
  - Purificar todo **daña** OPS: 69.55 % con 60 % y 70.39 % con 80 %.
  - **[Inferencia]** Sin defensa, la exactitud ya es alta con 60 % de veneno (91.78 %), coherente con el fenómeno de protección parcial. La ganancia de detectar+purificar es pequeña ahí (+0.4) y más visible con 80 % (+3.1).

#### P21.7 Caso aplicado
*Una empresa arma un dataset de 50 000 imágenes descargadas de muchos sitios; sospecha que parte está protegida con UEs y quiere usar solo lo aprovechable.*
- Con IF puede separar con HTER ~1.5–8 % si la fracción protegida es grande y el método es EM u OPS (Tabla 1). Luego aplica DiffPure solo a lo marcado (90.61 % con 80 % protegido, vs 87.47 % sin hacer nada).
- Si la protección es AR o la fracción es pequeña (20 %), el detector casi no sirve (HTER 40–49 %). En ese caso, según la propia Tabla 5, entrenar directamente ya da ~92 %.

#### P21.8 Fortalezas y limitaciones
- **Fortalezas:**
  - no necesita datos limpios de confianza ni conocer el ataque;
  - explota una propiedad general de los atajos;
  - 5 tipos de UE, 4 ratios, 3 datasets;
  - muestra utilidad práctica con purificación.
- **Limitaciones inferidas:**
  - **[Demuestra]** fallas con ratios bajos y con AR;
  - **[Inferencia]** error de tabla en el promedio de CIFAR-100;
  - **[No especificado]** posible errata en la condición del pseudocódigo; valores de p y N_thre; costo (varios entrenamientos completos);
  - **[No especificado]** sin evaluación contra protecciones no basadas en atajos (p. ej., P19) ni contra atacantes adaptativos.
- **[Inferencia]** La afirmación de ser "el primer método de detección de UEs" se matiza con el Informe 1: **P5** (AAAI 2024) ya detectaba datasets envenenados (Simple Networks, Bias-shifting), aunque a nivel de dataset y no por muestra.

#### P21.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Primer método de detección de UEs; los UEs se aprenden más rápido; supera al segundo mejor en ~20 % HTER. |
| **Demuestra** | HTER 17.5 % vs 38.5/50.1 (Tabla 1); ablación (Tabla 4); detección + purificación (Tabla 5). |
| **Inferencia** | Error en el promedio de CIFAR-100; P5 del Informe 1 ya detectaba a nivel de dataset; ganancia práctica pequeña con poison ratios moderados. |
| **No especificado** | p, N_thre, costo, regla exacta del pseudocódigo. |

#### P21.10 Relación con los demás papers
- **P5 (Informe 1)** detecta a nivel de dataset con redes simples; **EPD (Informe 1, P4)** detecta protecciones convolucionales con un SVM sobre los bordes; **P23** detecta Fawkes con forense + SVM supervisado. IF es el único que detecta **por muestra sin supervisión**.
- Usa **DiffPure**, de la misma familia que **JCDP (P22)** y AVATAR.
- Comparte autores (Yi Yu, Alex Kot) con **P18**.
- **[Inferencia]** El principio "los atajos se aprenden rápido" es exactamente lo que P24 (WDS) y P26 formalizan como fortaleza de sus protecciones. Una protección muy "atajo" sería, por eso mismo, más detectable.

---

### P22 · Learnable Examples (LE / JCDP)

**Título:** *Unlearnable Examples Give a False Sense of Security: Piercing through Unexploitable Data with Learnable Examples*. **Autores:** Wan Jiang, Yunfeng Diao, He Wang, Jianxin Sun, Meng Wang, Richang Hong (Hefei Univ. of Technology, UCL, CAS). **Publicación:** ACM MM '23. **Rol:** ataque a la protección.

#### P22.1 Problema investigado
**[Afirma]** Los UEs dan una **falsa sensación de seguridad**: solo protegen si los datos protegidos son *todo* lo que el atacante tiene. En la práctica, el atacante puede reunir **otros datos no protegidos similares** (pocos, sin etiquetas, incluso de otra distribución) y usarlos para "quitar" la protección.

Críticas a los contraataques previos:
- **AT, AA** (*adversarial augmentations*) e **ISS** están ligados a un esquema de entrenamiento concreto: dejan los datos sin cambios y no sirven para otros usos, como aprendizaje no supervisado.
- **AVATAR** (difusión) entrena su modelo de difusión con los **datos limpios originales**, que en un escenario real no están disponibles.

#### P22.2 Propuesta
Definir una nueva amenaza, los **Learnable unauthorized Examples (LEs)**: UEs con la protección removida, utilizables como datos normales en cualquier esquema de entrenamiento.

Para obtenerlos propone **JCDP (Joint-conditional Diffusion Purification)**:
1. entrenar un DDPM incondicional con datos crudos no protegidos D_r;
2. proyectar los UEs al manifold aprendido mediante difusión + des-difusión guiada.

🔎 **Analogía.** Un restaurador nunca vio *tu* cuadro original, pero estudió cientos de cuadros del mismo estilo. Le das tu cuadro con una capa de barniz manchado; él lo cubre con una capa de "niebla", lo restaura pintando como sabe, y se asegura de que el resultado se parezca al cuadro que le diste en forma y en impresión general. El barniz manchado desaparece porque no forma parte de lo que él sabe pintar.

✅ **Explicación técnica correcta.**
- **"Niebla":** difundir el UE T_p pasos. La perturbación queda escalada por √ᾱ y sumergida en el ruido gaussiano (Eq. 8).
- **"Restaurar":** el proceso inverso del DDPM (Eq. 9).
- **"Parecido en forma e impresión":** la media de cada paso inverso se desplaza σ_t²(d₁ + d₂) (Eq. 16):
  - d₁ = −λ₁∇MSE(x*_t, x̃_t) es la guía de **píxel**;
  - d₂ = −λ₂∇‖Φ(x*_t) − Φ(x̃_t)‖₂ es la guía **perceptual**, con Φ = *features* tipo LPIPS.
- **Justificación:** como el UE es por definición imperceptible y cercano al limpio en píxeles, parecerse al UE implica parecerse al limpio.

#### P22.3 Funcionamiento técnico
- **Trade-off de T_p:** con pocos pasos la perturbación no se sumerge; con muchos se pierde la semántica.
  - **[Demuestra/Afirma]** Varias iteraciones de purificación con T_p pequeño funcionan mejor que una con T_p grande (Alg. 1, N iteraciones).
- **Fine-tuning del DDPM:** se inicializa con un DDPM entrenado en otro dominio no protegido y se ajusta con D_r. La convergencia pasa de 80 000 a 1000 pasos (Tabla 6).
- **Derivación bayesiana (Eq. 10–16):** aproximación de Taylor a la distribución condicional conjunta en pixel + percepción.
- **Datos crudos por defecto:** el **test set sin etiquetas** del mismo dataset (10 000 en CIFAR; 26 032 en SVHN), "sin solapamiento" con el training.

#### P22.4 Datos y resultado
- **Entrada:** UEs + un conjunto pequeño de datos crudos no protegidos y sin etiquetas (opcionalmente un DDPM preentrenado en otro dominio).
- **Salida:** dataset "aprendible" (LEs) utilizable con **entrenamiento estándar** o **aprendizaje no supervisado**.

#### P22.5 Evaluación
- **Datasets:** CIFAR-10, CIFAR-100, SVHN, Pets. **[Inferencia]** El texto dice "three widely adopted datasets" pero lista cuatro.
- **UEs supervisados:** EMN (sample-wise), EMN(C) (class-wise), REMN, LSP (L2 = 1.0), TAP (L∞ 8/255), todos con 100 % de veneno.
- **UEs no supervisados:** **CP** (*Contrastive Poisoning*) sobre SimCLR, MoCo v2 y BYOL; **UC/UC-CLIP** (16/255).
- **Comparación con:** AT, ISS, AA (K = 1) y AVATAR; augmentations; AdvCL, Cutout y ruido aleatorio en el caso no supervisado.
- **Clasificador:** ResNet-18.

#### P22.6 Resultados clave e interpretación

**Tabla P22-a (exactitud %, ResNet-18; P22 Tabla 1).**

| Dataset (limpio) | UE | Vanilla | AVATAR | ISS | AT | AA | **LE** |
|---|---|---|---|---|---|---|---|
| CIFAR-10 (95.3) | EMN | 21.2 | 91.0 | 93.0 | 84.8 | 90.8 | **93.1** |
| | EMN(C) | 20.7 | – | – | 85.0 | – | **94.0** |
| | REMN | 20.5 | 88.5 | **92.8** | 49.2 | 85.5 | 92.2 |
| | LSP | 15.0 | 85.7 | 82.5 | 80.2 | 84.9 | **92.4** |
| | TAP | 7.8 | 90.7 | 83.9 | 83.0 | – | **90.8** |
| CIFAR-100 (78.8) | EMN | 14.8 | 65.7 | 67.5 | 63.4 | 70.0 | **70.9** |
| | REMN | 10.9 | 64.9 | 57.3 | 27.1 | – | **65.3** |
| | LSP | 4.1 | 58.5 | 53.5 | 58.1 | 67.4 | **68.7** |
| SVHN (96.2) | EMN | 13.9 | 93.8 | 89.9 | 86.3 | 88.7 | **94.7** |
| | LSP | 7.3 | 83.8 | 92.2 | 80.2 | 92.6 | **93.3** |
| | TAP | 41.0 | 93.4 | – | – | – | **94.9** |

**Tabla P22-b (no supervisado, *linear probing*; P22 Tabla 4).**

| UE | Dataset / backbone | Limpio | Sin defensa | **LE** |
|---|---|---|---|---|
| CP | CIFAR-10 / SimCLR | 90.4 | 44.9 | **86.6** |
| CP | CIFAR-10 / MoCo v2 | 89.3 | 55.1 | **86.0** |
| CP | CIFAR-10 / BYOL | 92.2 | 59.6 | **85.7** |
| CP | CIFAR-100 / SimCLR | 63.6 | 34.7 | **57.4** |
| UC | Pets / SimCLR | 48.3 | 17.9 | **46.0** |
| UC-CLIP | Pets / SimCLR | 48.3 | 26.1 | **47.5** |

**Tabla P22-c (distribución distinta para entrenar el DDPM, sin fine-tuning; P22 Tabla 5).**

| Escenario | Dataset protegido | DDPM entrenado con | EMN | LSP |
|---|---|---|---|---|
| (1) similitud alta | CIFAR-10 | CIFAR-100 | 92.1 | 89.1 |
| (2) similitud media | CIFAR-100 | CIFAR-10 | 66.9 | 66.0 |
| (3) similitud baja | CIFAR-10 | SVHN | 89.3 | 85.6 |
| (3) similitud baja | CIFAR-100 | SVHN | 52.9 | 54.1 |

**Interpretación.**
- **[Demuestra]** LE es el mejor en casi todas las combinaciones de la Tabla 1. La única excepción que reconocen es REMN en CIFAR-10 (ISS 92.8 vs 92.2). **[Inferencia]** Algunas victorias son por márgenes mínimos (EMN CIFAR-10: 93.1 vs 93.0 de ISS; TAP CIFAR-10: 90.8 vs 90.7 de AVATAR).
- **[Demuestra]** LE es consistente entre tipos de UE, mientras cada otro método tiene un "punto débil":
  - AT con REMN: 49.2 %;
  - ISS con LSP;
  - en la Tabla 3, GRAY falla con LSP (82.5 % en CIFAR-10, 44.6 % en CIFAR-100) y JPEG con EMN/REMN (~81–82 %).
- **[Demuestra]** Las augmentations clásicas no rompen los UEs: con EMN el máximo es 56.3 % (FastAutoAugment); con REMN, 26.8 %; con LSP, 25.9 % (Tabla 2).
- **[Demuestra]** Es el único método evaluado en **UEs no supervisados**: en CIFAR-10/SimCLR, LE 86.6 % frente a AdvCL 79.3 %, Cutout 47.7 % y ruido aleatorio 54.1 %.
- **[Demuestra]** **Tolera distribuciones distintas:** un DDPM entrenado en SVHN (dígitos) purifica CIFAR-10 hasta 89.3 %. **[Afirma]** Los autores especulan que los términos de guía conjunta "arrastran" la media hacia la distribución objetivo, pero reconocen que es "difícil identificar teóricamente la causa".
- **[Demuestra]** Ablación (EMN; Tabla 6):

  | FT | JC | Pasos | CIFAR-10 | CIFAR-100 |
  |---|---|---|---|---|
  | ✗ | ✗ | 80 000 | 90.6 | 69.0 |
  | ✓ | ✗ | 10 000 | 91.9 | 69.7 |
  | ✓ | ✓ | 10 000 | **93.1** | **70.9** |

  La guía conjunta aporta ~1.2 puntos y el fine-tuning acelera la convergencia.
- **[Afirma]** LE reduce el tiempo de entrenamiento ~70 % frente a métodos basados en AT. **[No especificado]** Los tiempos están en el material suplementario, no incluido.
- **[Inferencia]** Hay diferencias entre "limpio" en la Tabla 1 (CIFAR-10 95.3; CIFAR-100 78.8) y en la Tabla 3 (94.7; 74.5).
- **[Inferencia] Posible sesgo en el escenario por defecto:** el DDPM se entrena con el **test set sin etiquetas** y luego el clasificador se evalúa en ese mismo test set. El modelo de difusión "vio" las imágenes de evaluación, aunque sin etiquetas. El paper no lo discute. Los escenarios de la Tabla 5 (otra distribución) no tienen este problema y muestran resultados algo menores pero aún altos.

#### P22.7 Caso aplicado
*Una plataforma de fotos de mascotas protege todo su catálogo con UC-CLIP. Un competidor no puede entrenar bien con esas fotos (17.9–26.1 % en Pets con SimCLR).*
- Descarga además unos miles de fotos de mascotas no protegidas de otros sitios, entrena un DDPM y purifica el catálogo con JCDP.
- Su modelo SimCLR llega a 47.5 %, prácticamente el 48.3 % del limpio (Tabla 4).
- Según la Tabla 5, ni siquiera necesitaría fotos de mascotas: con un DDPM entrenado en otro tipo de imágenes la recuperación seguiría siendo alta (89.3 % en CIFAR-10 con un DDPM de SVHN).

**Lectura práctica:** la protección por UE no puede considerarse segura mientras existan datos similares sin proteger.

#### P22.8 Fortalezas y limitaciones
- **Fortalezas:**
  - redefine la amenaza de forma realista (el atacante tiene otros datos);
  - método independiente del esquema de entrenamiento;
  - evaluación en supervisado **y** no supervisado;
  - estudio de distribución distinta;
  - ablación clara.
- **Limitaciones inferidas:**
  - **[No especificado]** λ₁, λ₂, T_p, N y tiempos (en el suplementario);
  - **[Inferencia]** posible sesgo por entrenar el DDPM con el test set;
  - **[Inferencia]** márgenes mínimos en algunos casos;
  - **[No especificado]** sin protección parcial ni UEs adaptativos diseñados contra difusión;
  - un solo clasificador (ResNet-18) en supervisado;
  - **[Inferencia]** resultados algo menores en CIFAR-100 (65–71 % vs 78.8 limpio).

#### P22.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Los UEs dan falsa seguridad; LE es el primer contraataque generalizable a supervisado y no supervisado; ~70 % menos tiempo que AT. |
| **Demuestra** | Tablas 1–6: mejor o empatado con el mejor en casi todo; rompe CP y UC; tolera distribución distinta; ablación. |
| **Inferencia** | Márgenes mínimos frente a ISS/AVATAR en algunos casos; posible fuga del test set al DDPM; inconsistencias de "limpio". |
| **No especificado** | Hiperparámetros de purificación, tiempos, UEs adaptativos. |

#### P22.10 Relación con los demás papers
- **JCDP es el mismo componente de purificación** que usa **CMCR** (Informe 1, P3) para romper MEM en el caso multimodal. **[Inferencia]** P22 es la base de ese ataque.
- Rompe **UC/UC-CLIP (P20)**.
- **Ninguna** de las protecciones del bloque (P15–P20, P24–P26) se evalúa contra él. **[Inferencia]** Es la prueba pendiente más importante para todas ellas.
- **AVATAR**, su competidor directo, aparece en el Informe 1 como el ataque que más afecta a IRP (P10).
- Comparte con **P21** el uso de difusión. P21 muestra que **purificar todo** puede dañar (OPS 69.55 %), mientras JCDP sobre todo el dataset no reporta ese problema. **[Inferencia]** Son métodos distintos (JCDP guiado vs DiffPure) y no se evaluó OPS en P22.
- La idea de "las protecciones solo protegen a quien las adopta" conecta con P20 (Fig. 6a) y P19.

---

### P23 · Detección de cloaking (ELA + img2vec + SVM)

**Título:** *Unmasking the Threat: Detecting Cloaking Attacks in Facial Recognition*. **Autores:** Xavier Merino, Pei Zhou, Michael King (Florida Institute of Technology). **Rol:** detección (desde el punto de vista del operador de reconocimiento facial).

#### P23.1 Problema investigado
**Fawkes** añade perturbaciones imperceptibles (*cloaking*) a fotos de rostros antes de publicarlas. Si esas fotos entran en el entrenamiento de un sistema de reconocimiento facial (FR), lo **envenenan** para ciertas identidades; es un *clean-label poisoning*. Para la empresa que opera el FR:
- reentrenar o robustecer el modelo es caro;
- la robustez frente a un algoritmo de cloaking no garantiza robustez frente a futuros algoritmos (§I).

**[Afirma]** Como pocos usuarios usan cloaking (Fawkes tendría ~840 000 descargas), el impacto en un modelo entrenado con millones de fotos es marginal y afecta a pocas identidades. Por eso conviene **detectar y excluir** las fotos con cloaking antes de entrenar.

**Fawkes** (según la descripción de P23 §II.A):
1. elige imágenes objetivo de un dataset público;
2. calcula *feature vectors* del rostro;
3. selecciona el objetivo más disímil;
4. calcula el *cloak*;
5. minimiza la distancia de *features* al objetivo con restricción de presupuesto (DSSIM ≤ 0.007 por defecto).

Tiene niveles **low/mid/high**; el nivel alto genera artefactos visibles (manchas azuladas, "cráteres"; según Janssen y Carton, citados por P23).

#### P23.2 Propuesta
Un detector binario "con cloaking / sin cloaking" basado en **forense digital**: **ELA → img2vec (ResNet-18) → PCA → SVM**.

🔎 **Analogía.** Un experto en billetes no necesita saber quién los falsificó: pasa cada billete por una luz especial que revela diferencias de impresión invisibles a simple vista, y aprende cómo se ven los billetes falsos bajo esa luz.

✅ **Explicación técnica correcta.**
- **ELA (*Error Level Analysis*)** es la "luz especial": recomprime la imagen a distintos niveles y compara con la original; las zonas modificadas muestran niveles de error de compresión anómalos.
- Sobre la imagen ELA se extraen *features* con **img2vec** (ResNet-18 preentrenada; 512 dimensiones), se reducen con **PCA** y un **SVM** clasifica.

#### P23.3 Funcionamiento técnico
- **Pipeline:** imagen → ELA → ResNet-18 (img2vec) → vector 512-d → PCA → SVM.
- **Elección de ResNet-18:** "convergencia más rápida que arquitecturas más profundas" (§IV).
- **Validación:** *stratified 5-fold cross-validation*.

#### P23.4 Datos y resultado
- **Entrada:** foto de rostro.
- **Salida:** etiqueta "cloaked / original".
- **Requiere** ejemplos etiquetados de fotos con y sin cloaking del algoritmo a detectar (Fawkes).
- **Datos:** **PubFig83** (83 identidades × 100 fotos = 8300). Cada foto se "cloakeó" en los niveles low, mid y high → 33 200 imágenes en total.

#### P23.5 Evaluación
- **Esquema:** 5-fold CV por nivel y con niveles mezclados.
- **Métricas:** accuracy, precision, recall, F1, ROC AUC (§1.13).
- **No hay** baselines ni otros algoritmos de cloaking.

#### P23.6 Resultados clave e interpretación

**Tabla P23-a (medias de 5 folds; P23 Tablas I–IV).**

| Nivel | Accuracy | Precision | Recall | F1 | ROC AUC |
|---|---|---|---|---|---|
| Low | 0.9304 | 0.9458 | 0.9132 | 0.9291 | 0.9676 |
| Mid | 0.9924 | 0.9950 | 0.9899 | 0.9924 | 0.9978 |
| High | 0.9960 | 0.9964 | 0.9955 | 0.9960 | 0.9994 |
| Mezclado | 0.9744 | 0.9894 | 0.9763 | 0.9828 | 0.9916 |

**Interpretación.**
- **[Demuestra]** Detectar Fawkes con forense + aprendizaje supervisado es muy preciso. Incluso el nivel bajo, el más imperceptible, se detecta con 93 % de exactitud. Las desviaciones estándar son ≤ 0.015.
- **[Afirma]** "97 % across a wide variety of perturbation budgets" (abstract), lo que corresponde al escenario mezclado.
- **[Afirma] Limitaciones declaradas:**
  - ELA analiza toda la imagen, así que portadas de revista, montajes o correcciones de color alrededor del rostro, o fotos de alta resolución con un rostro pequeño, pueden causar errores;
  - proponen ELA solo sobre la región facial;
  - solo detecta Fawkes;
  - la detección **no** permite identificar a la persona; se plantea un "fusion model" como trabajo futuro.
- **[Inferencia]** En la Fig. 3, los vectores PCA de originales y cloaked se superponen en 2 componentes. La separación ocurre en más dimensiones (no visualizadas).

#### P23.7 Caso aplicado
*Una empresa de control de acceso reentrena cada mes su modelo con fotos nuevas recolectadas de la web y nota que para algunas personas el sistema falla cada vez más.*
- Antes de reentrenar, pasa cada foto por ELA + img2vec + SVM y excluye las marcadas como cloaked. Según la Tabla IV, detectaría ~97–98 % de las fotos con cloaking de Fawkes (recall 0.9763) con pocas falsas alarmas (precision 0.9894).
- Si las fotos vienen protegidas por **otra** herramienta (p. ej., PyrFawkes del Informe 1 o Segue de P15), el detector no fue probado y podría no funcionar.

**Lectura ética:** desde el punto de vista del usuario, este detector **anula** su intento de protección; desde el de la empresa, evita el envenenamiento.

#### P23.8 Fortalezas y limitaciones
- **Fortalezas:**
  - método simple, barato y reutilizable (ResNet-18 preentrenada + SVM);
  - resultados estables entre folds;
  - plantea el problema desde la perspectiva del operador.
- **Limitaciones inferidas:**
  - **[No especificado]** número de componentes PCA, kernel e hiperparámetros del SVM, calidad JPEG de la ELA;
  - **[No especificado]** si los folds separan por foto original. Si la versión limpia y la cloaked de la **misma foto** caen en folds distintos, el clasificador puede aprovechar la casi-duplicación. **[Inferencia]** Riesgo de resultados optimistas;
  - **[No especificado]** balance de clases en el escenario mezclado (¿8300 originales frente a 24 900 cloaked?);
  - **[Inferencia]** sin baselines, sin atacante adaptativo, un solo dataset de celebridades;
  - **[Inferencia]** no mide el efecto final sobre la exactitud del FR tras filtrar.

#### P23.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | 97 % de exactitud en diversos presupuestos; filtrar evita reentrenar; Fawkes resiste la detección de anomalías (según sus autores). |
| **Demuestra** | Tablas I–IV: 93–99.6 % de exactitud por nivel; 97.44 % mezclado. |
| **Inferencia** | Riesgo de fuga por casi-duplicados entre folds; generalización a otros cloaks desconocida. |
| **No especificado** | Hiperparámetros, partición exacta, balance, venue. |

#### P23.10 Relación con los demás papers
- Es la contraparte directa de **PyrFawkes** (Informe 1, P12), que mejora a Fawkes. P12 *afirma* robustez a detección de cloaks sin experimentos; P23 muestra que al menos Fawkes **sí** es detectable con un detector supervisado.
- Junto con **P21 (IF)**, **EPD** (Informe 1, P4) y **P5** (Informe 1) forma la línea de **detección**. P23 es supervisado (necesita ejemplos del ataque); P21 es no supervisado.
- **[Inferencia]** Segue (P15) y el caso de rostros de P26 (PubFig) no se evaluaron contra un detector de este tipo.

---

### P24 · WDS (Wavelet Domain Steganography)

**Título:** *Wavelet Domain Steganography for Robust Unlearnable Data*. **Autores:** Jiahao Li, Yiqiang Chen, Yunbing Xing, Yang Gu (ICT, Chinese Academy of Sciences). **Publicación:** *Pattern Recognition* (número especial). **Rol:** protección surrogate-free.

#### P24.1 Problema investigado
- Los métodos con sustituto (EM) son frágiles ante AT.
- REM es robusto pero inestable y caro.
- Los métodos sin sustituto (OPS, de un píxel, y **CUDA**, convolucional) son robustos y eficientes, pero **sacrifican imperceptibilidad**: cambios abruptos de píxeles, difuminado, pérdida de altas frecuencias y bandas oscuras en los bordes en CUDA (Fig. 2).

**Pregunta:** ¿se puede tener una protección sin sustituto, imperceptible, eficiente y robusta a AT?

#### P24.2 Propuesta
**WDS**: escribir de forma **esteganográfica** información específica de la clase en el **dominio wavelet**.

**Wavelet (transformada de Haar)** — 🔎 *Analogía:* separar una canción en "melodía" (graves) y "detalles" (agudos en distintas direcciones), sabiendo además *en qué momento* suena cada cosa. ✅ *Técnico:* una DWT de un nivel descompone cada canal en cuatro sub-bandas de la mitad de tamaño:
- **cA** (aproximación, baja frecuencia);
- **cH** (detalle horizontal);
- **cV** (detalle vertical);
- **cD** (detalle diagonal).

A diferencia de Fourier, conserva la **localización espacial** (Eq. 5).

**Esteganografía** — 🔎 *Analogía:* escribir un mensaje con tinta invisible dentro de una carta normal. ✅ *Técnico:* incrustar un código (aquí, la etiqueta en binario) en la imagen de forma recuperable por un extractor entrenado, minimizando el cambio visual.

#### P24.3 Funcionamiento técnico
1. **Codificación de etiqueta:** y → vector de k = ⌈log₂ C⌉ bits (4 en CIFAR-10). Un *upsampler* (FC + interpolación bilineal + conv; Tabla B.8) lo convierte en una imagen B_y del tamaño de la sub-banda.
2. **DWT de Haar** de un nivel.
3. **Inyección en cA y cD** (no en cH/cV):
   - cH y cV codifican bordes; alterarlos produce blur o ruido visible;
   - cD tiene poca energía;
   - usar dos sub-bandas da **redundancia**.

   Dos autoencoders tipo U-Net (Tabla B.9) reciben Concat(sub-banda, B_y) y producen c̃A y c̃D (Eq. 7).
4. **IDWT:** x' = IDWT(c̃A, cH, cV, c̃D) (Eq. 9).
5. **Extracción:** tres extractores convolucionales recuperan los bits desde c̃A, c̃D y x', con pérdida BCE (Eq. 12–14).
6. **Objetivo conjunto (Eq. 15):** L = L_stego + λ·L_wavelet, con L_wavelet = (‖x'−x‖² + ‖c̃A−cA‖₁ + ‖c̃D−cD‖₁)/3 y λ = 0.9. Adam lr 1e-3, batch 500, 100 épocas.

🔎 **Analogía.** Escribes con tinta invisible el número de "materia" en dos rincones específicos de cada página (uno de fondo y otro de textura fina), asegurándote de que un lector especial pueda leerlo y de que a simple vista la página se vea igual. Quien estudie de esas páginas aprende a leer el número invisible en lugar del contenido.

✅ **Explicación técnica correcta.**
- Los "rincones" son las sub-bandas cA y cD.
- El "lector especial" son los extractores; obligar a que el código sea **recuperable** garantiza que la señal esté presente y sea consistente por clase.
- El modelo víctima, por *shortcut learning*, aprende la señal de clase (casi invariante dentro de cada clase) en lugar de la semántica.
- **No hay** clasificador víctima en el bucle: es *surrogate-free*.

**Análisis teórico (Apéndice A):**
- Con supuestos (varianza intra-clase de la perturbación ≈ 0, codificación inyectiva, subespacios de *features* ortogonales y margen γ), la señal S cumple I(S;Y) = H(Y).
- La pérdida de entrenamiento puede llevarse a ~0 usando solo F_pert.
- En test limpio se da ε_test ≥ 1 − 1/K − δ_bias.

**[Inferencia]** Es un argumento heurístico bajo supuestos fuertes; no explica formalmente la robustez a AT.

#### P24.4 Datos y resultado
- **Entrada:** dataset etiquetado. Necesita la etiqueta para el código; no necesita modelo víctima.
- **Salida:** imágenes con el código de clase incrustado.
- **[No especificado]** Presupuesto Lₚ (ε) de la perturbación: la imperceptibilidad se controla solo con las pérdidas de reconstrucción.

#### P24.5 Evaluación
- **Datos:** CIFAR-10, CIFAR-100, ImageNet-100.
- **Modelos:** ResNet-18, VGG-16, WRN-34-10, DenseNet-121.
- **Baselines:** CUDA, REM (ρ_u 8/255, ρ_a 4/255), EM, TAP, NTGA, SEM, PUE, GUE.
- **Pruebas:**
  - AT en varias normas y radios;
  - protección parcial;
  - augmentations (Mixup, Cutout, CutMix, AutoAugment, Orthogonal Reg., grayscale, randomized smoothing σ = 0.5, JPEG calidad 90);
  - ataque de frecuencia **RSKFF**;
  - sesgo de frecuencia;
  - tiempo;
  - calidad visual (SSIM, LPIPS, F_diff).
- **[Afirma]** "Five runs with controlled seeds" (sin desviaciones reportadas).

#### P24.6 Resultados clave e interpretación

**Tabla P24-a (ResNet-18, exactitud %; AT = L∞ 4/255; P24 Tabla 1).**

| Dataset | Entrenamiento | Limpio | EM | REM | SEM | GUE | CUDA | **WDS** |
|---|---|---|---|---|---|---|---|---|
| CIFAR-10 | ERM | 94.66 | 13.20 | 27.09 | 13.00 | 13.25 | 18.48 | **11.36** |
| | AT | 89.51 | 88.62 | 48.16 | 31.92 | 52.79 | 44.40 | **15.27** |
| CIFAR-100 | ERM | 76.27 | **1.60** | 10.14 | 1.95 | 8.35 | 12.69 | 1.62 |
| | AT | 64.50 | 63.43 | 27.10 | 20.25 | 43.02 | 34.34 | **6.73** |
| ImageNet-100 | ERM | 80.66 | **1.26** | 13.74 | 4.10 | 11.54 | 8.96 | 2.47 |
| | AT | 66.62 | 63.40 | 41.66 | 37.82 | 38.90 | 38.68 | **19.86** |

**Tabla P24-b (radios de AT, CIFAR-10; P24 Tabla 4).**

| Entrenamiento | CUDA | **WDS** |
|---|---|---|
| ERM | 18.48 | 11.36 |
| AT L∞ 4/255 | 44.40 | 15.27 |
| AT L∞ 8/255 | 32.85 | 16.30 |
| AT L∞ 16/255 | 19.32 | 18.14 |
| AT L2 0.25 | 39.05 | 16.16 |
| AT L2 0.50 | 51.19 | 20.75 |
| AT L2 0.75 | 51.14 | 24.17 |

**Tabla P24-c (protección parcial, CIFAR-10, ResNet-18; P24 Tabla 3).**

| % protegido | 20 | 40 | 60 | 80 | 100 |
|---|---|---|---|---|---|
| Solo la parte limpia (ERM) | 93.75 | 92.56 | 89.77 | 84.30 | – |
| WDS mezclado (ERM) | 91.07 | 89.25 | 82.98 | **71.33** | 11.36 |
| Solo la parte limpia (AT) | 88.17 | 86.76 | 85.07 | 79.41 | – |
| WDS mezclado (AT) | 87.93 | 86.04 | 83.51 | 81.60 | 15.27 |

**Interpretación.**
- **[Demuestra]** Bajo AT, WDS es claramente el más fuerte en los tres datasets: 15.27 / 6.73 / 19.86 frente a 31.92 / 20.25 / 37.82 del mejor competidor (SEM).
- **[Demuestra]** Transfiere entre arquitecturas con AT (Tabla 2): VGG-16 14.27, WRN 18.35, DenseNet-121 16.51 (vs CUDA 42.98–53.02 y SEM 31.42–77.85).
- **[Inferencia]** La afirmación "consistently achieves the lowest test accuracy across all datasets" **no es exacta** bajo ERM: EM es más bajo en CIFAR-100 (1.60 vs 1.62) e ImageNet-100 (1.26 vs 2.47).
- **[Demuestra]** Resiste RSKFF, un ataque de frecuencia que rompe CUDA (Tabla 5):

  | Dataset | CUDA (AT → RSKFF) | WDS (AT → RSKFF) |
  |---|---|---|
  | CIFAR-10 | 44.40 → 78.53 | 15.27 → 28.73 |
  | CIFAR-100 | 34.34 → 53.33 | 6.73 → 8.92 |
  | ImageNet-100 | 38.68 → 43.08 | 19.86 → 20.93 |

  **[Inferencia]** La columna rotulada "REM" en esa tabla contiene en realidad los valores de ERM (errata).
- **[Demuestra]** Augmentations (CIFAR-10): Mixup 13.07, Cutout 13.51, CutMix 14.02, AutoAugment 15.79, Orthogonal Reg. 15.40, grayscale 17.03, randomized smoothing 16.24, JPEG-90 17.04 %. **[Inferencia]** JPEG con calidad 90 es una compresión suave; ISS suele usar calidades más bajas, que no se probaron.
- **[Demuestra] Protección parcial (Tabla P24-c):**
  - bajo ERM, la mezcla queda **por debajo** del entrenamiento con solo la parte limpia (80 %: 71.33 vs 84.30); **[Inferencia]** los datos WDS no solo dejan de aportar, **perjudican**;
  - bajo AT, la mezcla con 80 % queda **por encima** del limpio solo (81.60 vs 79.41).
- **[Demuestra]** Sesgo de frecuencia (Tabla 6): cA 12.88, cH 16.61, cV 14.59, **cD 10.09**, Fourier 17.75. **[Inferencia]** El caption dice "solo se preserva una sub-banda", pero el texto habla de "esteganografía solo en cD": descripción ambigua del experimento.
- **[Demuestra]** Tiempo (Tabla 7): WDS 0.35 h (CIFAR) y 2.55 h (ImageNet-100), frente a CUDA 10.8 s y REM 22.6 h / 51.2 h.
- **[Demuestra]** Calidad visual vs CUDA (Tabla D.11):

  | Método | SSIM | LPIPS | F_diff |
  |---|---|---|---|
  | **WDS** | 0.9129 | 0.0157 | 0.7974 |
  | CUDA | 0.8893 | 0.0254 | 1.0159 |

  **[Inferencia]** Solo se compara con CUDA, no con métodos aditivos de 8/255.
- **[Inferencia]** Las cifras de los baselines coinciden exactamente con las de SEM (P16) y con las de CUDA/KBS del Informe 1 (p. ej., REM 48.16, SEM 31.92, CUDA 18.48/44.40, RSKFF-CUDA 78.53): parecen tomadas de la literatura. En la Tabla 3, SEM al 100 % aparece como 31.29, frente a 31.92 en la Tabla 1.

#### P24.7 Caso aplicado
*Un estudio fotográfico publica su catálogo y teme que un tercero entrene con AT 8/255 para "limpiar" atajos.*
- Con CUDA la exactitud del tercero sería 32.85 %; con WDS, 16.30 % (Tabla 4), y las fotos se verían más naturales (SSIM 0.91 vs 0.89).
- Si el tercero aplica RSKFF, CUDA cae a 78.53 % y WDS se mantiene en 28.73 % (Tabla 5).

**Pregunta abierta:** qué pasaría frente a una purificación por difusión como la de P22, que no se evaluó.

#### P24.8 Fortalezas y limitaciones
- **Fortalezas:**
  - surrogate-free y rápido;
  - muy robusto a AT en varios radios y normas;
  - robusto a RSKFF y augmentations;
  - mejor calidad visual que CUDA;
  - análisis de sesgo de frecuencia.
- **Limitaciones declaradas (trabajo futuro):** eficiencia en gran escala, aleatoriedad de la perturbación frente a ataques de remoción, extensión cross-modal.
- **Limitaciones inferidas:**
  - **[No especificado]** ε y métricas visuales frente a métodos aditivos;
  - **[No especificado]** sin pruebas contra difusión (P22), detección (P21) ni un atacante adaptativo que conozca el método;
  - **[Inferencia]** afirmación de superioridad no exacta en ERM;
  - **[Inferencia]** erratas de tabla;
  - **[Inferencia]** teoría heurística.

#### P24.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Mejor equilibrio imperceptibilidad–unlearnability; siempre la menor exactitud; las DNN tienen sesgo hacia ciertas frecuencias. |
| **Demuestra** | Tablas 1–7 y D.11: fuerte robustez a AT, transferencia, RSKFF, augmentations, tiempo y calidad vs CUDA. |
| **Inferencia** | No es el mejor en ERM en CIFAR-100/ImageNet-100; mezcla perjudicial en ERM; cifras de baselines reutilizadas; erratas. |
| **No especificado** | ε; resultados frente a difusión o detección; desviaciones de las 5 corridas. |

#### P24.10 Relación con los demás papers
- **Hermano metodológico de KBS** (Informe 1, P9). Ambos codifican un código binario de clase con autoencoders + extractores + pérdidas de reconstrucción; KBS en la magnitud y fase de Fourier, WDS en sub-bandas wavelet. **[Inferencia]** Bajo AT 4/255 en CIFAR-10, WDS (15.27) mejora a KBS (20.97, cifra del Informe 1).
- **CUDA** (Informe 1, P2) es su principal comparación.
- Supera a **SEM (P16)** bajo AT.
- **[Inferencia]** Su codificación de la etiqueta en bits recuerda la *side information* de 16 bits de **Segue (P15)**; ambos crean un atajo ligado a la clase de forma "aprendida".
- **[Inferencia]** Por la Fig. 6a de P20 y la Tabla 3 de P24, WDS sería uno de los pocos métodos que perjudican en mezcla (con ERM), lo que lo acerca al objetivo de **P19** por otra vía.

---

### P25 · ARMOR

**Título:** *ARMOR: Shielding Unlearnable Examples against Data Augmentation*. **Autores:** Xueluan Gong, Yuji Wang, Yanjiao Chen, Haocheng Dong, Yiming Li, Mengyuan Sun, Shuaike Li, Qian Wang, Chen Chen (NTU, Wuhan Univ., Zhejiang Univ.). **Rol:** protección robusta a data augmentation. **Nota:** este paper aparece en dos carpetas del repositorio (misma obra).

#### P25.1 Problema investigado
**[Afirma]** "Somos los primeros" en mostrar que la **data augmentation**, un preprocesamiento rutinario, viola la privacidad que dan los UEs.

**[Demuestra]** Con DeepAA en CIFAR-10 (Tabla I), sin augmentation → con augmentation:

| Modelo | EMIN | REM |
|---|---|---|
| VGG-16 | 25.59 → 62.69 | 29.61 → 47.58 |
| ResNet-18 | 25.08 → 56.05 | 24.15 → 43.25 |
| WRN-34-10 | 21.38 → **66.14** | 24.50 → 50.38 |

El abstract resume esto como "de 21.3 % a 66.1 %".

**[Demuestra]** Con otras augmentations (Tabla II):
- PuzzleMix eleva REM en Mini-ImageNet de 26.81 a 64.95 %;
- Mixup eleva EMIN en VGG-Face de 1.46 a 47.96 %.

#### P25.2 Propuesta
**ARMOR**: generar el ruido error-minimizing **a través de la augmentation**: min_θ E[min_δ L(f(A(x+δ, p)), y)] (Eq. 6). Usa un sustituto f y una **política de augmentation sustituta** p que imitan las del atacante, con tres bloques:
1. **Sustituto con módulo non-local:** ResNet-18 + bloque non-local en los dos primeros bloques residuales.
2. **Selección de la augmentation sustituta por clase:** maximiza la alineación de gradientes.
3. **PGD con paso adaptativo.**

🔎 **Analogía.** Si sabes que el copión va a fotocopiar tus apuntes girados, recortados o mezclados con otros, no basta con esconder la pista en la versión original: la diseñas para que siga funcionando **después** de esas transformaciones, y practicas con las transformaciones que más se parecen a las que él usaría.

✅ **Explicación técnica correcta.**
- El ruido δ se optimiza para minimizar la pérdida del sustituto **sobre la imagen ya aumentada** A(x+δ, p).
- La política p se elige por clase con el criterio de DeepAA: la operación o del espacio de búsqueda (Tabla III) que maximiza la similitud coseno entre el gradiente de un lote y el del lote aumentado (Eq. 9–10).
- El módulo non-local amplía el campo receptivo. **[Afirma]** "La augmentation induce a ampliar el campo receptivo".

#### P25.3 Funcionamiento técnico
- **Módulo non-local (Eq. 7–8):** bᵢ = (1/C(a)) Σⱼ F(aᵢ, aⱼ)·G(aⱼ), con F gaussiana embebida exp(φ(aᵢ)ᵀϕ(aⱼ)) y G lineal. Cada posición se actualiza con información de **todas** las demás.
- **Selección de augmentation (Alg. 1):**
  - un modelo auxiliar f' (ResNet-18, preentrenado 10 épocas en limpio) se actualiza con 10 lotes perturbados por iteración;
  - para cada clase i se elige la operación que maximiza CS(∇x₁, ∇A(x₂, o));
  - espacio de búsqueda (Tabla III): Identity, ShearX/Y, TranslateX/Y, Rotate, AutoContrast, Invert, Equalize, Solarize, Posterize, Contrast, Color, Brightness, Sharpness, Flips, Cutout, Crop.
- **Generación del ruido (Alg. 2, Eq. 11–12):** δ ← δ − α_t·sign(∇L(f(A(x+δ, p)), y)).
  - Paso adaptativo α_t = γ / (c + √Σᵢ nᵢ,t), donde nᵢ,t es un promedio móvil del cuadrado de la norma del gradiente: pasos más pequeños cuando los gradientes son grandes.
  - PGD con 10 rondas y paso 0.8; criterio de parada como EMIN.
- **Ruido sample-wise** por defecto; el class-wise es el promedio del ruido de cada clase.
- **Presupuesto:** ε = 8/255 para todos los métodos (§V.C). Ver la inconsistencia en P25.6.

#### P25.4 Datos y resultado
- **Entrada:** dataset privado completo (el defensor no conoce el modelo ni la augmentation del atacante).
- **Salida:** dataset protegido.
- **Datos:**
  - CIFAR-10, CIFAR-100;
  - Mini-ImageNet (40 000 / 10 000, 224 px);
  - VGG-Face (200 identidades; 53 811 / 10 762).

#### P25.5 Evaluación
- **Víctimas:** VGG-16, ResNet-18/50, DenseNet-121, WRN-34-10, EfficientNet, ResNeXt-50, MobileNet.
- **Baselines:** ruido gaussiano, EMAX, UTAP/CTAP, NTGA, EMIN, REM (radio AT 2).
- **Augmentation del atacante:** DeepAA por defecto; también Mixup, Feature Distillation, PuzzleMix y FastAA.
- **Otras pruebas:** class-wise; ablación; sustituto; ε; protección parcial por clases; tiempo; AT (Super-convergence, Propagate).

#### P25.6 Resultados clave e interpretación

**Tabla P25-a (sample-wise, exactitud %; sin augmentation / con DeepAA; P25 Tablas IV–V).**

| Dataset / modelo | EMIN | REM | **ARMOR** |
|---|---|---|---|
| CIFAR-10 / ResNet-18 | 25.08 / 56.05 | 24.15 / 43.25 | **12.85 / 28.84** |
| CIFAR-10 / WRN-34-10 | 21.38 / 66.14 | 24.50 / 50.38 | **11.94 / 30.73** |
| CIFAR-100 / ResNet-18 | 15.19 / 39.92 | 13.18 / 27.07 | **12.62 / 25.10** |
| CIFAR-100 / ResNet-50 | 13.24 / 42.45 | **11.91** / 25.74 | 12.63 / **24.04** |
| Mini-ImageNet / ResNet-18 | 14.98 / 58.06 | 26.81 / 44.46 | **3.72 / 11.69** |
| VGG-Face / ResNet-18 | 1.46 / 42.63 | 3.21 / 36.86 | **0.28 / 23.10** |

**Tabla P25-b (otras augmentations, CIFAR-10; P25 Tabla X).**

| Augmentation | Limpio | EMIN | CTAP | REM | **ARMOR** |
|---|---|---|---|---|---|
| FastAA | 94.81 | 44.00 | 42.71 | 38.40 | **22.91** |
| Mixup | 95.03 | 33.65 | 32.78 | 31.58 | **21.15** |
| PuzzleMix | 95.37 | 40.52 | 45.53 | 40.43 | **17.93** |
| Feature distillation | 92.91 | 31.17 | 68.11 | 32.67 | **16.13** |
| DeepAA | 94.49 | 56.05 | 40.20 | 43.25 | **28.84** |

**Tabla P25-c (adversarial training: REM / ARMOR; P25 Tabla XVIII).**

| Dataset | Super-convergence (FGSM) | Propagate ρ=1 | Propagate ρ=2 |
|---|---|---|---|
| CIFAR-10 | 43.24 / **14.16** | **10.10** / 12.90 | **31.47** / 34.38 |
| CIFAR-100 | **14.83** / 26.54 | 12.06 / **11.75** | **14.69** / 28.18 |
| Mini-ImageNet | **6.78** / 9.32 | **3.84** / 6.90 | **4.44** / 8.17 |
| VGG-Face | 70.77 / **32.35** | **17.72** / 35.26 | **27.19** / 35.72 |

**Interpretación.**
- **[Demuestra]** ARMOR es el más fuerte con DeepAA en todos los datasets y arquitecturas de la Tabla V, y con las cinco augmentations de la Tabla X. **[Inferencia]** Con DeepAA sigue en 28–31 % en CIFAR-10: la augmentation todavía recupera ~16 puntos respecto del caso sin augmentation (12.85 → 28.84).
- **[Demuestra]** Sin augmentation también es el mejor casi siempre. **[Inferencia]** Hay excepciones:
  - CIFAR-100 / ResNet-50: REM 11.91 vs ARMOR 12.63;
  - en class-wise, EMIN es mejor en Mini-ImageNet / MobileNet (1.29 vs 1.46) y VGG-Face / EfficientNet (0.43 vs 0.48).
- **[Inferencia] Frente a AT, ARMOR es peor que REM en 9 de las 12 configuraciones de la Tabla XVIII.** Solo gana en Super-convergence en CIFAR-10 y VGG-Face y en Propagate ρ = 1 en CIFAR-100 (por 0.31). Los radios probados son pequeños (1–2). El texto y el abstract dicen "ARMOR is also robust to adversarial training", lo que la evidencia respalda solo parcialmente.
- **[Demuestra]** Ablación (Tablas VIII–IX, con DeepAA, CIFAR-10 VGG-16): Base 46.76 → +non-local 40.89 → +paso adaptativo 29.59. **[Inferencia]**
  - En otros casos el non-local aporta poco (ResNet-18: 42.24 → 41.92) y el paso adaptativo es la mejora principal.
  - No hay ablación que quite la **selección de augmentation**: "Base" ya la incluye. Su aporte solo puede inferirse comparando con EMIN (56.05 → 42.24 en ResNet-18 con DeepAA).
- **[Demuestra]** Efecto de ε (Tablas XIII–XIV, con augmentation):
  - CIFAR-100: 60.07 % con ε = 4 y 25.10 % con ε = 8;
  - **VGG-Face: 95.60 / 70.68 / 55.56 / 23.10 % con ε = 4 / 8 / 12 / 16**.

  **[Afirma]** "ε de 16 para VGG-Face" por su alta separabilidad lineal. **[Inferencia] Inconsistencia de presupuesto:** los resultados principales de ARMOR en VGG-Face (Tablas IV–V: 0.28 % y 23.10 % con ResNet-18) coinciden con los de ε = 16 en las Tablas XIII–XIV, mientras §V.C dice que ε = 8/255 para baselines y ARMOR. Con ε = 8 y augmentation, ARMOR en VGG-Face daría 70.68 %, peor que EMIN (42.63) y REM (36.86).
- **[Demuestra]** Protección parcial por clases (Tablas XV–XVI): la exactitud en las clases protegidas baja más cuantas más clases se protegen; las clases no protegidas mantienen ~93–98 %. Con DeepAA y 2 clases protegidas de CIFAR-10, ARMOR sample-wise deja 28.65 % en esas clases (EMIN 50.80 %).
- **[Demuestra]** Tiempo (Tabla XVII, CIFAR-10): ARMOR 0.50 h + 1.90 h, frente a EMIN 0.56 h, REM 38 h + 2.5 h, NTGA 17.67 h y TAP 9.5 h.
- **[Inferencia] Inconsistencias internas de cifras:**
  - Tabla IX (VGG-Face, completo): ResNet-50 31.36, EfficientNet 8.97 y ResNeXt 20.79, frente a 28.36 / 20.79 / 18.97 en la Tabla V;
  - Tabla VIII Mini-ImageNet DenseNet-121: 4.90 vs 6.00;
  - sustituto ResNet-18 con augmentation en CIFAR-10: 30.84 (Tabla XII) vs 28.84 (Tabla V);
  - el texto dice "less than 2.25 % (Mini-ImageNet) and less than 0.17 % (VGG-Face)", cuando esas cifras son los **mínimos**, no máximos.

#### P25.7 Caso aplicado
*Una agencia de modelos publica fotos de su catálogo protegidas con EMIN (25.08 % de exactitud para un ResNet-18 de un recolector).*
- Si el recolector entrena con DeepAA, sube a 56.05 %; con ARMOR quedaría en 28.84 % (CIFAR-10, Tabla V).
- Si el recolector usa AT tipo Propagate con ρ = 2, ARMOR (34.38 %) protege **peor** que REM (31.47 %) (Tabla XVIII).
- En rostros (VGG-Face), la protección efectiva con augmentation requiere ε = 16 (23.10 %), con mayor visibilidad del ruido.

#### P25.8 Fortalezas y limitaciones
- **Fortalezas:**
  - identifica una amenaza práctica y muy común (DA);
  - evaluación extensa (4 datasets, 5 augmentations, 8 arquitecturas, sample- y class-wise, parcial por clases, tiempos);
  - método relativamente barato (horas, frente a decenas de horas de REM).
- **Limitaciones inferidas:**
  - **[Inferencia]** robustez a AT solo parcial y con radios pequeños;
  - **[Inferencia]** inconsistencia de ε en VGG-Face y varias cifras;
  - **[No especificado]** ablación de la selección de augmentation, métricas de imperceptibilidad (solo Fig. 2 visual);
  - **[No especificado]** sin pruebas contra ISS/JPEG, difusión (P22) ni detección (P21);
  - **[Inferencia]** se asume que el atacante usa DeepAA "principalmente".

#### P25.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Primeros en revelar la amenaza de DA; ARMOR reduce la exactitud "hasta 60 % más" que los baselines; robusto a AT. |
| **Demuestra** | DA rompe EMIN/REM (Tablas I–II); ARMOR mejor bajo 5 DA (Tablas V, VII, X); ablación (VIII–IX); tiempos (XVII). |
| **Inferencia** | Peor que REM bajo AT en 9/12; ε=16 en VGG-Face; inconsistencias de tablas; falta ablación de la selección de augmentation. |
| **No especificado** | Imperceptibilidad cuantitativa; defensas de compresión/difusión/detección; venue. |

#### P25.10 Relación con los demás papers
- Lleva al extremo la idea del Informe 1, P1 (augmentations/transformaciones como forma de romper UEs).
- Contrasta con **P22**, que muestra que las augmentations clásicas (Cutout, Mixup, CutMix, FastAA) **no** rompen EMN/REMN/LSP (Tabla 2 de P22: ≤56.3 %). **[Inferencia]** Las cifras no son comparables directamente: P25 usa DeepAA y otros setups, y su EMIN con FastAA en CIFAR-10 queda en 44.00 %, cerca del 56.3 % de P22.
- **SEM (P16)** también integra transformaciones (EOT) en la generación, pero apunta a AT, no a DA.
- **WDS (P24)** reporta robustez a Mixup, Cutout, CutMix y AutoAugment (13–16 %) sin diseño específico para DA. **[Inferencia]** Las protecciones "de atajo de clase" parecen naturalmente más resistentes a DA que las sample-wise de EM.
- Ruido gaussiano, EMAX y TAP como baselines débiles coinciden con lo observado en P26 (Tabla 5).

---

### P26 · Dominant image features (clean-label)

**Título:** *Clean-label poisoning attack with perturbation causing dominant features*. **Autores:** Chen Zhang, Zhuo Tang, Kenli Li (China University of Petroleum, Hunan University). **Publicación:** *Information Sciences* (Elsevier, 2023). **Rol:** envenenamiento clean-label usado como protección, con extensión a backdoor.

#### P26.1 Problema investigado
Los ataques de envenenamiento contra DNNs son poco estudiados por dos razones: la optimización binivel es cara y el funcionamiento de las DNN no se entiende del todo. Los ataques previos (influence functions, label flipping, backdoors) suelen producir imágenes visiblemente corruptas o detectables. Los autores buscan un **envenenamiento clean-label de todo el training set**, con perturbaciones imperceptibles, que haga que modelos bien entrenados fallen en datos limpios.

Lo motivan con dos escenarios:
- una empresa que entrega datos a un tercero para modelado y no quiere que el modelo sirva con datos reales;
- la protección de fotos de rostros personales.

#### P26.2 Propuesta
Basada en la interpretación de las DNN como **extractores de rasgos**: si se suman rasgos **fuertes** (dominantes) de una clase a una imagen de otra, los fuertes dominan la clasificación.

Los autores generan **"dominant image features"**: una perturbación universal por clase r_t que hace que **cualquier** imagen se clasifique como t. Luego suman a cada imagen de la clase c el r_t de otra clase t ≠ c.

🔎 **Analogía.** Si a todas las fotos de perros les pegas, casi invisible, el "olor" característico de los gatos (y a las de gatos el de los pájaros, etc.), un perro olfateador entrenado con esas fotos aprenderá a asociar "olor a gato" con "perro". Ante un perro real, sin ese olor, no sabrá qué hacer.

✅ **Explicación técnica correcta.**
- El "olor" es r_t, una perturbación universal **dirigida** hacia la clase t, calculada contra un modelo entrenado en limpio.
- Al sumarlo a todas las imágenes de c (etiquetadas como c), el modelo víctima aprende la correspondencia r_t ↔ c, muy fácil de aprender: entrenamiento ≈ 100 %.
- En el test limpio no hay r_t, y la imagen "se comporta como ruido" para el modelo.

#### P26.3 Funcionamiento técnico
1. **Entrenar** una DNN f en datos limpios (idealmente la del propio usuario).
2. **Generar r_t para cada clase (Alg. 1):** buscar r con ‖r‖ₚ ≤ ε tal que f(x+r) = t para x de cualquier clase (Eq. 3).
   - Pérdida tipo C&W: L_t = max(max_{c≠t} Z_c(x+r) − Z_t(x+r), −κ) (Eq. 4), con Z los logits y κ la confianza.
   - Optimización con Adam sobre lotes muestreados (500 imágenes en MNIST/FMNIST; 1000 en CIFAR-10), con proyección r ← r/‖r‖ₚ·ε.
   - **Parada:** cuando la "strength ratio" sth(r) supera δ = 0.75, donde sth(r) es la fracción de imágenes **del test set** que f clasifica como t tras sumar r (Eq. 5).
3. **Envenenar:** x_p = x_c + r_t con c ≠ t, eligiendo la asignación al azar (Eq. 6).
4. **Entrenar y evaluar** el modelo víctima con los datos envenenados.

**Extensión a backdoor (§3.3):** se usa r_t como trigger. Se envenena el 20 % de la clase objetivo (2 % del dataset) con su propio r_t y, en test, se suma r_t a imágenes de otras clases. Según los autores, r_t por sí solo no transfiere como trigger a otros modelos, por eso hace falta envenenar.

#### P26.4 Datos y resultado
- **Entrada:** dataset etiquetado completo, un modelo entrenado en limpio y acceso a imágenes (incluido el test) para la parada.
- **Salida:** dataset envenenado con perturbaciones class-wise (K perturbaciones). **[Afirma]** Es menos eficiente si K es grande.

#### P26.5 Evaluación
- **Datasets y modelos:**
  - MNIST: LR, SVM lineal, SVM-RBF, Random Forest, LeNet;
  - Fashion-MNIST: CNNs pequeñas, VGG16;
  - CIFAR-10 e ImageNet-100 (100 primeras clases): GoogLeNet, VGG19, ResNet18, DenseNet121, RegNetX200, DPN92; 200 épocas SGD.
- **Presupuestos L∞ (escala 0–1):**
  - MNIST 0.3, FMNIST 0.1;
  - CIFAR-10 y ImageNet-100: 0.05 y 0.02 según el orden del texto (ver inconsistencia).
- **Baselines (Tabla 5):** TensorClog, DeepConfuse, EM, Alignment, REM, LSP.
- **Defensas (Tabla 6):** AT, augmentations, DP-SGD.
- **Calidad visual:** PSNR.
- **Escenario real:** reconocimiento facial con PubFig.
- **Backdoor:** comparación de ASR.

#### P26.6 Resultados clave e interpretación

**Tabla P26-a (exactitud media en test limpio; P26 Tablas 2–4).**

| Dataset | Limpio (media) | Envenenado (media) | Observación |
|---|---|---|---|
| MNIST | 93.97 | 19.91 | LR el menos afectado (41.52 %) |
| Fashion-MNIST | 90.73 | 14.18 | VGG16 10.76 % |
| CIFAR-10 | 92.12 | **10.64** | 10.01–12.37 % en 6 arquitecturas |
| ImageNet-100 | 58.47 | **1.80** | 1.33–2.26 % |

**Tabla P26-b (comparación de envenenamientos, CIFAR-10, RegNet, ε=0.05; P26 Tabla 5).**

| Método | Exactitud % |
|---|---|
| Limpio | 95.43 |
| Ruido aleatorio | 90.16 |
| TensorClog | 82.36 |
| Alignment | 51.18 |
| DeepConfuse | 28.54 |
| EM (Unlearnable Examples) | 20.75 |
| REM | 15.18 |
| LSP (Synthetic Perturbations) | 13.54 |
| **Dominant features** | **10.54** |

**Tabla P26-c (defensas, CIFAR-10, RegNet, ε=0.05, 5 corridas; P26 Tabla 6).**

| Defensa | Exactitud % |
|---|---|
| AT (parámetros no especificados) | 19.75 |
| Augmentation básica | 10.66 |
| Cutout / Mixup / CutMix | 10.58 / 10.82 / 10.60 |
| Fast AutoAugment | 12.26 |
| DP-SGD (clip 1.0, ruido 0.005) | 18.86 |

**Interpretación.**
- **[Demuestra]** El entrenamiento con datos envenenados alcanza ~100 % de exactitud de entrenamiento y cae a nivel de azar en test limpio. Es coherente con la interpretación de atajo/rasgo dominante.
- **[Demuestra]** La protección no depende del modelo usado para generarla: con VGG la media es 10.54 % y con ResNet 10.69 % (Tabla 7).
- **[Demuestra]** Una strength ratio δ ≥ 0.7 es necesaria (Fig. 5: δ ≈ 0.35 → ~53 %; 0.5 → ~36 %; ≥ 0.75 → ~11 %). Bastan ~1000 imágenes para estimar r (Fig. 6). La sth(r) en CIFAR-10 varía por clase, de 0.54 (frog) a 0.76 (airplane) (Tabla 1).
- **[Demuestra]** PSNR: MNIST 31.50 dB, FMNIST 35.92 dB, CIFAR-10 40.24 dB. **[Afirma]** En MNIST la perturbación es perceptible "pero aceptable".
- **[Demuestra]** Escenario facial (PubFig, 58 797 fotos de 200 personas): con un LeNet para generar y un VGG-19 como reconocedor, las identidades protegidas quedan en ~8 % y las demás en ~92 % (Fig. 7). **[Inferencia]** El texto dice que se protegen "30 identidades" y luego que el training tiene "150 identidades limpias y 50 envenenadas": cifras incompatibles.
- **[Demuestra]** Backdoor clean-label (ResNet-18, CIFAR-10; Tabla 8): ASR de 84.6 %, frente a BadNets 24.5 %, Label-Consistent 58.2 % y Zhao 80.8 %.
- **[Inferencia] Presupuestos inconsistentes:**
  - el texto asigna 0.05 a CIFAR-10 y 0.02 a ImageNet-100 en la lista de §4.3, pero en "Perturbation Stealthiness" y en la Fig. 4 dice que CIFAR-10 usa 0.02;
  - las Tablas 5–6 usan 0.05 para CIFAR-10;
  - 0.05 ≈ 12.75/255, por encima del estándar 8/255, y P26 dice haber usado el mismo ε para todos los baselines, que normalmente se reportan a 8/255.
- **[Inferencia]** En la Tabla 5, "Ours" con RegNet = 10.54 %, pero la Tabla 4 da 10.01 % para RegNetX200 (10.54 es el valor de ResNet18).
- **[Inferencia]** REM = 15.18 % coincide con el valor de REM **sin AT** en P16 (y con RUE en CIFAR-10 en P15), lo que sugiere cifras tomadas de la literatura y no regeneradas con ε = 0.05.
- **[Inferencia]** La parada usa el **test set** (sth(r) se mide en D_test): el método "ve" imágenes de evaluación al crear la perturbación, aunque solo use las predicciones del modelo f.
- **[Inferencia]** El paper declara "No data was used for the research", pese a usar datasets públicos.

#### P26.7 Caso aplicado
*Una empresa debe entregar sus 50 000 imágenes de productos a un proveedor externo que construirá un modelo para otro cliente, pero no quiere que ese modelo funcione con imágenes reales de sus productos.*
- Entrena un DenseNet propio, calcula los 10 rasgos dominantes y suma a cada clase los de otra.
- El proveedor obtendría ~100 % en entrenamiento pero ~10.6 % en test real (CIFAR-10, Tabla 4).
- Aunque use AT, quedaría en ~19.75 % (Tabla 6).

**Advertencia:** la misma técnica, con 2 % de datos envenenados, implanta un **backdoor** con ASR 84.6 % (Tabla 8). Es un ejemplo de herramienta de doble uso.

#### P26.8 Fortalezas y limitaciones
- **Fortalezas:**
  - método simple (sin binivel), eficiente y transferible entre arquitecturas;
  - evaluación desde modelos clásicos (SVM, RF) hasta DNNs;
  - resiste AT y augmentations en CIFAR-10;
  - análisis de sensibilidad (ε, δ, tamaño de muestra);
  - extensión a backdoor.
- **Limitaciones declaradas:** poco eficiente si hay muchas clases (K perturbaciones).
- **Limitaciones inferidas:**
  - **[Inferencia]** ε inconsistente y mayor que el estándar; usa el test set en la parada; cifras contradictorias (identidades en PubFig, RegNet);
  - **[No especificado]** parámetros de AT;
  - **[No especificado]** sin pruebas contra ISS/JPEG, difusión (P22) ni detección (P21, P23);
  - **[Inferencia]** protección parcial solo en el caso facial, sin control "solo limpios".

#### P26.9 Cuadro epistemológico
| Tipo | Contenido |
|---|---|
| **Afirma** | Clean-label "verdadero"; supera a los envenenamientos previos; las defensas previas son ineficaces; verifica la visión de las DNN como extractores de rasgos. |
| **Demuestra** | Tablas 2–8 y Figs. 4–7: caídas a nivel azar en 4 datasets; mejor que 6 baselines a ε=0.05; resiste AT/augmentations/DP-SGD; ASR 84.6 %. |
| **Inferencia** | Comparaciones con ε mayor que el habitual; inconsistencias de presupuesto y de identidades; posible uso de cifras de baselines de la literatura. |
| **No especificado** | Configuración de AT; detalles de la asignación c→t; resultados contra purificación o detección. |

#### P26.10 Relación con los demás papers
- **Mismo grupo de autores** (Chen Zhang, Zhuo Tang; mismas afiliaciones) que **P11 del Informe 1** (envenenamiento de clustering con un "robust feature" universal). **[Inferencia]** P26 es la versión para clasificación supervisada de la misma idea de "rasgo universal dominante".
- Conceptualmente cercano a **TAP/CTAP** (envenenamiento adversarial dirigido) y a **LSP** (atajos sintéticos por clase).
- Como WDS (P24) y KBS (Informe 1, P9), crea un patrón por clase. A diferencia de ellos, el patrón se obtiene de un modelo entrenado (perturbación adversarial universal), no de un código binario.
- Su resistencia a AT (19.75 %) es comparable a WDS (15.27 %), pero con ε mayor y parámetros de AT desconocidos. **[Inferencia]** La comparación no es directa.
- Su caso facial (PubFig) conecta con P15 (Segue), P23 (detección de cloaking) y PyrFawkes (Informe 1, P12).

---

## 4. Análisis comparativo entre los 13 papers (y con el Informe 1)

### 4.1 Taxonomía por rol y por dominio

| Rol | Papers | Dominio |
|---|---|---|
| **Protección (genera UEs)** | P15 Segue, P16 SEM, P17 T2UE, P18 TUE, P19 Bayes, P20 UC, P24 WDS, P25 ARMOR, P26 Dominant | Imágenes (P16, P19, P24, P25, P26), rostros (P15; caso facial en P26), multimodal (P17), video (P18), alta resolución sin etiquetas (P20) |
| **Ataque a la protección** | P22 LE/JCDP | Supervisado y no supervisado |
| **Defensa del entrenador (aprender a pesar del veneno)** | P14 TPD | Clasificación con ruido de etiquetas |
| **Detección** | P21 IF (UEs en general), P23 ELA+SVM (Fawkes) | Imágenes / rostros |

**[Inferencia]** Respecto del Informe 1, el bloque está más orientado a **protecciones** (9 de 13) y abre dominios nuevos (video, multimodal "zero-contact", label-agnostic). La familia convolucional (CUDA y derivados) que dominaba el Informe 1 aparece aquí solo como baseline (P24) o como precedente metodológico (KBS → WDS).

### 4.2 ¿Qué necesita cada método? (supuestos de amenaza)

| Paper | ¿Necesita la imagen? | ¿Etiquetas? | Sustituto | Presupuesto declarado | ¿Qué conoce del atacante? |
|---|---|---|---|---|---|
| P15 Segue | Sí | Sí (o K) | ResNet-18 | L∞ 8/255 | Nada (capa de distorsión genérica) |
| P16 SEM | Sí | Sí | ResNet-18 con AT | L∞ 8/255 (ρ_a 4/255) | **Radio de AT** (ρ_r = ρ_a) |
| P17 T2UE | **No** (solo texto) | No (texto) | CLIP congelado | L∞ 8/255 | Nada |
| P18 TUE | Sí + **bbox** | No | SiamFC | **No especificado** | Nada |
| P19 Bayes | Sí | Sí | ResNet-18 (embeddings) | L∞ 8/255 | Nada; funciona en mezclas |
| P20 UC | Sí | **No** | ResNet-50 o CLIP | L∞ **16/255** | Nada; robusto a re-etiquetado |
| P24 WDS | Sí | Sí (código) | **Ninguno** | **No especificado** | Nada |
| P25 ARMOR | Sí | Sí | ResNet-18 + non-local | L∞ 8/255 (16 en VGG-Face, ver §6) | Que usará **augmentation** |
| P26 Dominant | Sí | Sí | Modelo entrenado en limpio | L∞ 0.05 (≈12.75/255) en CIFAR-10 | Nada |
| P22 LE (ataque) | UEs | No | DDPM entrenado con otros datos | — | Datos no protegidos similares |
| P21 IF (detección) | Dataset mixto | Sí | ResNet-18 | — | Nada (no supervisado) |
| P23 (detección) | Fotos | Ejemplos etiquetados de Fawkes | ResNet-18 (img2vec) | — | **El algoritmo de cloaking** |
| P14 TPD (defensa) | Dataset | Ruidosas | ResNet34 | ε propio 0.1/0.2 | Nada |

**[Inferencia]** Las comparaciones entre protecciones están condicionadas por el presupuesto: P20 (16/255), P26 (0.05) y los métodos sin ε declarado (P18, P24) no son directamente comparables con los que usan 8/255.

### 4.3 Matriz de robustez: ¿contra qué se evaluó cada protección?

Leyenda: **✓** protege en lo evaluado · **~** protege parcialmente o con excepciones · **✗** falla · **—** no evaluado.

| Protección | AT | Data augmentation | Compresión / filtros (JPEG, gris, paso-bajo) | Purificación por difusión | Detección | Protección parcial | Otros |
|---|---|---|---|---|---|---|---|
| P15 Segue | ✓ (16.5 % a 4/255 con ρ_d=4) | ✓ (Fig. 4, sin cifras en el md) | ✓ JPEG (Fig. 4, sin cifras) | — | — | — | Transferencia arquitecturas ✓ |
| P16 SEM | ~ (falla en CIFAR-100 VGG-16/DN-121) | — | ✓ filtros paso-bajo | — | — | ✗ (80 % → 82.98 %) | Sensible a ρ_r ≠ ρ_a |
| P17 T2UE | — | ✓ (CutOut/MixUp/AutoAug) | — | — | — | ~ (80 % → 68.44 %, sin control) | Más débil que EM |
| P18 TUE | — | — | — | — | — | — | Trackers ✓; HIPTrack ~ |
| P19 Bayes | ~ (74.5 %) | — | — | — | — | **✓** (69.68 % vs 91.16 %) | Todo protegido: 28.12 % |
| P20 UC | — | ✓ (Mixup/CutMix/Cutout; suavizado 24.26 %) | — | **✗ (P22: 46.0/47.5 %)** | — | ✗ (Fig. 6a) | Re-etiquetado ✓; SimCLR ✓ |
| P24 WDS | **✓** (15–24 % en varios radios) | ✓ | ✓ gris 17.03; ~ JPEG solo calidad 90 | — | — | ~ (ERM perjudica; AT no) | RSKFF ✓ |
| P25 ARMOR | ~ (peor que REM en 9/12) | **✓** (5 augmentations) | ✓ feature distillation | — | — | ~ (por clases) | ε=16 en VGG-Face |
| P26 Dominant | ✓ (19.75 %, parámetros no dados) | ✓ | — | — | — | ~ (caso facial sin control) | DP-SGD ✓; backdoor |

**Lectura.**
- **[Inferencia]** No hay **ninguna** celda "✓" en la columna de **purificación por difusión**: el único dato es un **✗** (UC frente a P22). P22 además rompe EMN, REMN, LSP, TAP y CP.
- **[Inferencia]** La columna de **detección** está vacía para las protecciones. P21 muestra que EM, OPS, LSP y REM son detectables cuando son mayoría (≥ 40 %).
- **[Inferencia]** Las dos defensas que cada paper elige para "demostrar robustez" suelen ser las que su diseño ataca:
  - SEM y WDS → AT;
  - ARMOR → augmentation;
  - UC → re-etiquetado;
  - P19 → mezcla.

  La robustez cruzada (contra las defensas que el método no anticipa) casi nunca se evalúa.

### 4.4 Protección parcial: la evidencia del bloque

| Paper | Configuración | Mezclado (UE + limpio) | Control "solo limpio" | ¿Los protegidos dañan? |
|---|---|---|---|---|
| P16 SEM | CIFAR-10, AT 2/255, 80 % protegido | 82.98 % | No reportado | No se puede saber |
| P17 T2UE | CIFAR-10 class-wise, 80 % | 68.44 % | No reportado | No se puede saber |
| P19 Bayes | CIFAR-10, 50 % | **69.68 %** | 91.16 % | **Sí** [Demuestra] |
| P19 (EM, citado) | CIFAR-10, 50 % | 92.51 % | 91.16 % | No (ayudan un poco) |
| P20 UC | Pets, n clases limpias | ≈ control | ≈ (Fig. 6a) | No |
| P21 (sin defensa) | CIFAR-10, 60 % / 80 % (promedio 5 UEs) | 91.78 / 87.47 % | No reportado | — |
| P24 WDS (ERM) | CIFAR-10, 80 % | **71.33 %** | 84.30 % | **Sí** [Inferencia] |
| P24 WDS (AT) | CIFAR-10, 80 % | 81.60 % | 79.41 % | No |
| P25 ARMOR | Clases protegidas (por clase) | Baja la exactitud solo en las clases protegidas | Clases no protegidas ~93–98 % | Protege solo lo protegido |

**[Inferencia]** El Informe 1 concluía que los datos protegidos "dejan de aportar pero no dañan". Este bloque muestra **dos excepciones**: P19 (por diseño) y WDS bajo ERM (sin que el paper lo destaque). También muestra que bajo AT incluso WDS vuelve al patrón "no daña". Para la mayoría de métodos, la pregunta sigue sin respuesta porque falta el control "solo limpio".

### 4.5 Generadores: qué condiciona cada uno y cuánto cuesta

| Paper | Arquitectura | Condición (*side information*) | Granularidad | Costo reportado |
|---|---|---|---|---|
| P15 Segue | Encoder–decoder conv | Etiqueta en 16 bits / pseudo-etiqueta K-means | Por muestra, ligada a identidad | Inferencia 2.2 s (WebFace10); entrenamiento **no reportado** |
| P17 T2UE | SSA-GAN (SSCBN) | Embedding de texto CLIP + z | Class- o sample-wise según texto/z | 0.3 h (Flickr8k), **sin** el entrenamiento (300–500 épocas) |
| P18 TUE | DiT-S/8 (un paso) | Estado de la bbox | Por objetivo y *frame* | Entrenamiento 7 h, 124 MB |
| P20 UC | Encoder–decoder, uno por cluster | Ruido uniforme (por cluster) | Cluster-wise | **No reportado** (p generadores) |
| P24 WDS | Autoencoders U-Net + extractores | Código binario de clase | Clase, adaptado por muestra | 0.35 h (CIFAR), 2.55 h (ImageNet-100) |
| P16 SEM | Generador para la regeneración final (Alg. 4) | — | Sample-wise | 5.78 h total (CIFAR) |

**[Inferencia]** Los generadores trasladan el costo de "optimizar por imagen" a "entrenar una vez". Las cifras de eficiencia son comparables solo cuando incluyen el entrenamiento (P18, P24, P16).

### 4.6 Detección: P21 vs P23 (y el Informe 1)

| Aspecto | P21 IF | P23 ELA+SVM | Informe 1: P5 | Informe 1: P4 EPD |
|---|---|---|---|---|
| Unidad | Muestra | Muestra (foto) | Dataset | Muestra |
| Supervisión | No (usa solo el dataset mixto) | Sí (ejemplos de Fawkes) | No | Sí (SVM sobre bordes) |
| Principio | Los UEs se aprenden más rápido (atajo) | Huellas de compresión (forense) | Separabilidad lineal / bias-shifting | Bordes oscuros de la convolución |
| Ataques cubiertos | EM, REM, LSP, OPS, AR | Solo Fawkes | Varios venenos | Convolucionales |
| Punto débil | Poison ratio bajo; AR | Otros cloaks; posibles casi-duplicados entre folds | CUDA (bias-shifting) | — |

**[Inferencia]** Los detectores no supervisados (P21, P5) explotan la **propiedad que define** a un UE de atajo; los supervisados (P23, EPD) explotan **artefactos** de un método concreto. Una protección sin atajo (P19) o con un atajo de otra naturaleza (P26) no ha sido probada contra ninguno.

### 4.7 Del lado del entrenador: TPD (P14) vs LE (P22)

| Aspecto | P14 TPD | P22 LE/JCDP |
|---|---|---|
| Estrategia | Cambiar el **entrenamiento** | Cambiar los **datos** (purificar) |
| Datos extra | Ninguno | Datos crudos no protegidos (pocos, sin etiquetas) |
| Resultado | Un clasificador robusto | Un dataset reutilizable (supervisado o no supervisado) |
| Ataques evaluados | DeepConfuse + ruido de etiquetas | EMN, REMN, LSP, TAP, CP, UC |
| Fortaleza única | Maneja también etiquetas ruidosas | Independiente del esquema de entrenamiento; tolera otra distribución |

**[Inferencia]** P22 critica explícitamente el enfoque de "cambiar el entrenamiento" (AT, AA, ISS) porque no deja datos reutilizables. TPD pertenece a esa categoría, pero cubre un problema (ruido de etiquetas) que P22 no aborda.

### 4.8 Similitudes y diferencias clave (pares relevantes)

| Par | Similitud | Diferencia |
|---|---|---|
| **P16 SEM ↔ P25 ARMOR** | Ambos modifican EM/REM introduciendo transformaciones en el bucle de generación | SEM: ruido aleatorio uniforme contra **AT**; ARMOR: augmentation elegida por gradientes contra **DA** |
| **P16 SEM ↔ P24 WDS** | Ambos apuntan a robustez frente a AT | SEM usa sustituto con AT; WDS no usa sustituto. Bajo AT 4/255, WDS 15.27 vs SEM 31.92 (P24 Tabla 1) |
| **P15 Segue ↔ P24 WDS** | Codifican la **etiqueta en bits** y la convierten en perturbación | Segue: generador + sustituto (error-minimizing); WDS: esteganografía sin sustituto |
| **P15 ↔ P17 ↔ P18 ↔ P20** | Generadores condicionados | Condición: etiqueta / texto / bbox / cluster |
| **P17 T2UE ↔ P20 UC** | CLIP como sustituto; mismo coautor (X. Ma) | T2UE necesita texto, no imagen; UC necesita imagen, no etiqueta |
| **P19 ↔ P20** | Ambos cuestionan supuestos de los UEs (mezcla / etiquetas) | P19 sí resuelve la mezcla; UC la deja como limitación |
| **P21 ↔ P23** | Detección por muestra | No supervisada vs supervisada; atajo vs forense |
| **P22 ↔ P21** | Usan difusión para purificar | P22 purifica todo con guía conjunta; P21 detecta primero y purifica solo lo detectado |
| **P24 WDS ↔ P26** | Patrón por clase que domina el aprendizaje | Código esteganográfico vs perturbación adversarial universal |
| **P26 ↔ Informe 1, P11** | Mismos autores; "rasgo universal dominante" | Clasificación supervisada vs clustering |
| **P24 WDS ↔ Informe 1, P9 KBS** | Código binario de clase + autoencoders + extractores | Wavelet (cA, cD) vs Fourier (magnitud y fase) |
| **P22 ↔ Informe 1, P3 CMCR** | JCDP como purificador | P22 lo propone (imágenes); CMCR lo usa en multimodal junto con regeneración de texto |
| **P23 ↔ Informe 1, P12 PyrFawkes** | Fawkes como referencia | P23 lo detecta; P12 lo mejora |

### 4.9 Coherencia numérica entre papers

- **SEM / REM / CUDA:** las cifras de P16 (REM 48.16, SEM 31.92 con AT en CIFAR-10; SEM 13.00 con ERM) reaparecen idénticas en P24 (Tablas 1–2) y en KBS (Informe 1, P9). Las de CUDA (18.48 / 44.40) y RSKFF-CUDA (78.53) aparecen idénticas en P24 y en KBS. **[Inferencia]** Son cifras reutilizadas.
- **REM = 15.18 %:** en P16 es REM sin AT (CIFAR-10); en P15 es RUE en CIFAR-10; en P26 es REM con ε = 0.05 y RegNet. **[Inferencia]** Coincidencia sospechosa: probablemente se copió el número.
- **REM en tiempo:** P16 y P24 reportan 22.6 h en CIFAR-10. P25 reporta 38.00 h + 2.50 h (otra máquina). **[Inferencia]** Los costos dependen del hardware y no son comparables entre papers.
- **UC:** en P20 (Pets) es fuerte; en P17 (CIFAR-10 supervisado) es débil (79.41 %); en P22 se rompe con difusión. Son settings distintos y no se contradicen, pero muestran que la "fuerza" de un método depende del escenario.

### 4.10 Mapa de conexiones con el Informe 1

| Informe 2 | Conecta con Informe 1 | Tipo de conexión |
|---|---|---|
| P14 TPD | P7 VESPR | Defensas que combinan señales auto-supervisadas/contrastivas |
| P15 Segue | P8 GUE, P12 PyrFawkes | Generador; rostros |
| P16 SEM | P9 KBS (baseline), P5 (teoría de presupuestos) | Cifras compartidas; ρ_a ≤ ρ_u |
| P17 T2UE | P3 CMCR | MEM y su fragilidad textual |
| P19 Bayes | P2, P9, P10 (protección parcial) | Respuesta al problema de mezcla |
| P21 IF | P5, P4 EPD | Detección |
| P22 LE | P3 CMCR, P10 IRP (AVATAR) | Purificación por difusión |
| P23 | P12 PyrFawkes | Fawkes: detección vs mejora |
| P24 WDS | P9 KBS, P2 CUDA | Esteganografía por clase; comparación directa |
| P25 ARMOR | P1 (transformaciones rompen UEs) | Augmentation como amenaza |
| P26 | P11 (mismos autores) | Rasgo universal dominante |

---

## 5. Casos aplicados transversales

Los siguientes escenarios son **ilustrativos**. Combinan resultados de varios papers para mostrar cómo se leerían en la práctica. Las cifras citadas son las de cada paper en sus datasets de referencia; en un caso real los números serían distintos.

### 5.1 Una persona quiere proteger las fotos de su rostro

1. **Proteger.**
   - Con **Segue (P15)** obtiene protección en segundos y transferible a varias arquitecturas (10.50 % en WebFace10, incluso en Inception-V3).
   - Con **ARMOR (P25)** resistiría augmentation, pero en rostros (VGG-Face) necesita ε = 16 para ser eficaz con DeepAA (23.10 % vs 70.68 % con ε = 8).
2. **Riesgo de detección.** Si el recolector entrena un detector supervisado para esa herramienta, como hace **P23** con Fawkes (97 % de exactitud), puede filtrar sus fotos y la protección "desaparece" del dataset.
3. **Riesgo de purificación.** Si el recolector tiene muchas otras fotos de rostros sin proteger (lo habitual), puede entrenar un DDPM y aplicar **JCDP (P22)**. Ninguna de estas protecciones fue evaluada contra eso.
4. **Riesgo de ser minoría.** Si solo esta persona protege sus fotos dentro de un dataset grande, el resultado de **UC (P20, Fig. 6a)** sugiere que el resto del modelo no se ve afectado, y solo su identidad podría quedar protegida. El caso PubFig de **P26** (8 % para identidades protegidas vs 92 % el resto) va en esa línea.

**[Inferencia]** La protección de rostros por UE es útil frente a un recolector "ingenuo", pero frágil ante uno que detecta (P23) o purifica (P22).

### 5.2 Un creador de video y un servicio de pares imagen–texto

- **Video (P18):** el generador TUE protege videos de un objeto con gran efecto en la mayoría de trackers (SeqTrack 74.7 → 2.1 AO), pero menos en trackers que reutilizan modelos preentrenados congelados (HIPTrack 77.4 → 43.2) y en segmentación de video (~17–23 puntos). No se evaluó ninguna defensa.
- **Multimodal (P17):** sin entregar la imagen, el usuario obtiene una protección intermedia (Flickr30k Hit@10 49.3 → 18.2). Sigue siendo menor que la de EM con acceso a la imagen (5.5), y el texto enviado puede ser sensible. **[Inferencia]** El ataque de CMCR (Informe 1, P3), que purifica imágenes con JCDP y regenera textos, es la prueba pendiente.

### 5.3 Una empresa que entrena con datos de la web

1. **¿Hay UEs?** Con **IF (P21)** detecta UEs de tipo EM/OPS con HTER ≤ 11 % si son abundantes. Con AR o pocos UEs (20 %) el detector falla, pero en ese caso los UEs casi no afectan (P21 Tabla 5: ~92 % sin defensa con 60 % envenenado).
2. **¿Cómo aprovechar los UEs detectados?** Purificándolos con DiffPure (P21: 90.61 % con 80 % de veneno) o con **JCDP** usando datos propios (P22: 92–94 % en CIFAR-10).
3. **¿Y si además hay etiquetas erróneas?** **TPD (P14)** es la única opción evaluada en ese escenario combinado (CIFAR-10 simétrico κ = 0.4: 79.0 % vs 18.59 % de entrenamiento estándar).

**[Inferencia]** Para el entrenador, la combinación detección → purificación selectiva es la estrategia mejor respaldada por la evidencia de este bloque.

### 5.4 Un dueño de datos que sabe que su dataset se mezclará con datos limpios

- Con UEs tradicionales (EM), mezclar mitad/mitad no hace nada (92.51 % vs 91.16 % solo limpios; P19).
- **P19** reduce la exactitud a 69.68 % en esa mezcla.
- **WDS (P24)** también perjudica bajo ERM (80 % protegido: 71.33 % vs 84.30 %), pero no bajo AT.

**[Inferencia]** Si el escenario realista es la mezcla, la elección debería basarse en estos resultados y no en la exactitud con 100 % de datos protegidos, la métrica que la mayoría de papers destaca.

### 5.5 Advertencia de doble uso

**P26** muestra que la misma perturbación usada para "proteger" sirve, con 2 % de datos envenenados, para implantar un **backdoor** con ASR 84.6 %. **P23** recuerda que, para el operador del sistema, una protección de privacidad es un ataque de envenenamiento. **[Inferencia]** Toda evaluación de estas técnicas debería considerar ambos lados: la herramienta y su posible abuso.

---

## 6. Vacíos, inconsistencias detectadas y límites de este informe

### 6.1 Vacíos comunes del bloque

1. **Ninguna protección se evalúa contra la purificación por difusión con datos externos (P22)**, pese a que P22 (2023) rompe protecciones de las mismas familias. Afecta a P15, P16, P17, P18, P19, P24, P25 y P26.
2. **Casi ninguna protección se evalúa contra detección** (P21, P23).
3. **Presupuestos heterogéneos o ausentes:** P18 y P24 no declaran ε; P20 usa 16/255; P26 usa 0.05 en CIFAR-10; P25 usa 16/255 en VGG-Face.
4. **Falta de varianza:** la mayoría no reporta desviación estándar. Excepciones: P23 (5 folds), P16 (3 semillas, sin dispersión en las tablas principales), P26 Tabla 6 ("5 runs", solo medias), P24 ("five runs", solo medias).
5. **Control "solo limpio" ausente** en la mayoría de experimentos de protección parcial (§4.4).
6. **Material suplementario no incluido:** P15 (algunas cifras en figuras), P18 (presupuesto, EdgeBox), P19 (pruebas), P22 (hiperparámetros y tiempos), P16 (teoremas del apéndice).

### 6.2 Inconsistencias internas detectadas (por paper)

| Paper | Inconsistencia | Etiqueta |
|---|---|---|
| P14 | Caption de la Tabla 1 confuso sobre qué baselines se reportan; weight decay "15e-4" | [Inferencia] |
| P15 | Cifras de RUE distintas para el mismo caso (11.50 / 19.00 / 17.00); calendario de entrenamiento descrito de dos formas | [Inferencia] |
| P16 | En la Tabla 5 el texto ("S_es=1500 increases test acc by 4%") no coincide con las columnas | [Inferencia] |
| P17 | Sustituto ResNet-18 vs ResNet-50 entre tablas; "32 epochs" vs 300–500 épocas; "limpio" de la Tabla 7 muy distinto del de la Tabla 2 | [Inferencia] |
| P19 | Curva "No add-on" no monótona; caída con 99.99 % limpio sin varianza | [Inferencia] |
| P21 | El promedio de CIFAR-100 copia el de ImageNet; la tabla de ablación aparece rotulada como "Table 2"; posible errata en la condición del pseudocódigo | [Inferencia] / [No especificado] |
| P22 | "Three datasets" pero lista cuatro; "limpio" difiere entre las Tablas 1 y 3; el DDPM se entrena con el test set | [Inferencia] |
| P23 | Partición de folds no especificada respecto de pares limpio/cloaked de la misma foto | [No especificado] |
| P24 | "Siempre el más bajo" no se cumple en ERM; columna "REM" en la Tabla 5 = ERM; SEM 31.29 vs 31.92; caption vs texto en la Tabla 6 | [Inferencia] |
| P25 | ε = 8 declarado vs resultados de VGG-Face que corresponden a ε = 16; cifras de ablación ≠ tablas principales; "less than" con mínimos | [Inferencia] |
| P26 | ε de CIFAR-10 (0.05 vs 0.02); 30 vs 50 identidades en PubFig; "Ours" RegNet 10.54 vs 10.01; "No data was used" | [Inferencia] |

### 6.3 Límites de este informe

- **Fuente única:** solo se usaron los archivos Markdown del repositorio. Las conversiones a Markdown pueden haber alterado tablas o fórmulas. Ejemplos detectados: celdas desplazadas en tablas de P21, títulos de tabla mal asignados y símbolos ●/○ mal convertidos en P15.
- **Figuras:** cuando un resultado solo está en una figura, las cifras citadas son **aproximadas**, según la descripción textual incluida en el md (P19 Figs. 4–5; P20 Figs. 4–6; P26 Figs. 5–6; P18 Fig. 5). Se marcan con "~".
- **Cálculos propios:** el promedio de CIFAR-100 de P21 (Acc ≈ 89.7, HTER ≈ 14.2) y los conteos (p. ej., ARMOR peor que REM en 9/12) son cálculos míos sobre las tablas del md.
- **Duplicado de ARMOR:** se verificó que las dos carpetas corresponden al mismo artículo y se trató como uno solo (P25).
- **Venue:** cuando el md no muestra el lugar de publicación, se indica "no visible". Solo se menciona otro venue cuando lo cita otro paper del repositorio (UC como CVPR 2023 según P15).

---

## Anexo A. Archivos fuente

| ID | Carpeta / archivo Markdown |
|---|---|
| P14 | `Robust Learning with Adversarial Perturbations and Label Noise A Two-Pronged Defense Approach/Robust Learning with Adversarial Perturbations and Label Noise A Two-Pronged Defense Approach.md` |
| P15 | `Segue Sideinformation Guided Generative Unlearnable Examples for Facial privacy protection in real world/Segue Sideinformation Guided Generative Unlearnable Examples for Facial privacy protection in real world.md` |
| P16 | `Stable Unlearnable Example Enhancing the Robustness of Unlearnable Examples via Stable Error-Minimizing Noise/Stable Unlearnable Example Enhancing the Robustness of Unlearnable Examples via Stable Error-Minimizing Noise.md` |
| P17 | `T2UE Generating Unlearnable Examples from Text Descriptions/T2UE Generating Unlearnable Examples from Text Descriptions.md` |
| P18 | `Temporal Unlearnable Examples Preventing Personal Video Data from Unauthorized Exploitation by Object Tracking/Temporal Unlearnable Examples Preventing Personal Video Data from Unauthorized Exploitation by Object Tracking.md` |
| P19 | `Towards Provably Unlearnable Examples via Bayes Error Optimization/Towards Provably Unlearnable Examples via Bayes Error Optimization.md` |
| P20 | `Unlearnable Clusters Towards Label-Agnostic Unlearnable Examples/Unlearnable Clusters Towards Label-Agnostic Unlearnable Examples.md` |
| P21 | `Unlearnable Examples Detection via Iterative Filtering/Unlearnable Examples Detection via Iterative Filtering.md` |
| P22 | `Unlearnable Examples Give a False Sense of Security Piercing through Unexploitable Data with Learnable Examples/Unlearnable Examples Give a False Sense of Security Piercing through Unexploitable Data with Learnable Examples.md` |
| P23 | `Unmasking-the-Threat-Detecting-Cloaking-Attacks-in-Facial-Recognition/Unmasking-the-Threat-Detecting-Cloaking-Attacks-in-Facial-Recognition.md` |
| P24 | `Wavelet Domain Steganography for Robust Unlearnable Data/Wavelet Domain Steganography for Robust Unlearnable Data.md` |
| P25 | `armor_Shielding_Unlearnable_Examples_against/armor_Shielding_Unlearnable_Examples_against.md` (principal) y `armor Shielding Unlearnable Examples against/armor Shielding Unlearnable Examples against.md` (duplicado) |
| P26 | `clean label poisoning attack with perturbation causing dominant features/clean label poisoning attack with perturbation causing dominant features.md` |
