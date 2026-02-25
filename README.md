# Taller — Protocolo Chord en Sistemas P2P

## 📘 Información General

- **Asignatura:** Arquitectura de Nube y Sistemas Distribuidos  
- **Carrera:** Ingeniería de Sistemas e Informática  
- **Facultad:** Ingeniería en TIC  
- **Institución:** Universidad Pontificia Bolivariana  
- **Profesor:** Álvaro Enrique Ospina  
- **Estudiante:** Mariana Osorio Rojas  

---

## 🎯 Objetivo del Taller

Desarrollar un análisis técnico del protocolo **0** aplicado a un escenario definido, demostrando comprensión conceptual, matemática y arquitectónica del problema de localización en redes P2P.

---

# 🧩 Instrucciones del Taller

## 🔹 Parte A — Finger Table (30%)

1. Construir la *finger table* para el nodo indicado en el escenario base.
2. Aplicar correctamente las fórmulas:
   - \( start_i = (n + 2^{i-1}) \bmod 2^m \)
   - \( finger[i] = successor(start_i) \)
3. Presentar la tabla organizada y correctamente justificada.
4. Explicar cómo los intervalos garantizan cobertura exponencial.
5. Demostrar matemáticamente por qué el lookup en Chord es \( O(\log N) \).

---

## 🔹 Parte B — Lookup (25%)

1. Realizar el procedimiento de búsqueda (*lookup*) para las claves indicadas.
2. Aplicar estrictamente la regla de *closest preceding finger*.
3. Justificar cada salto realizado entre nodos.
4. Considerar correctamente los casos de *wrap-around*.
5. Presentar la ruta completa hasta encontrar el sucesor final.

---

## 🔹 Parte C — Join y Actualización (20%)

1. Simular la unión de un nuevo nodo al anillo.
2. Determinar su sucesor y predecesor.
3. Explicar el proceso de actualización mediante:
   - `stabilize()`
   - `notify()`
   - `fix_fingers()`
4. Construir la finger table inicial del nuevo nodo.
5. Analizar cómo el sistema converge nuevamente a consistencia.

---

## 🔹 Parte D — Análisis Arquitectónico (15%)

1. Comparar Chord con el modelo Cliente/Servidor.
2. Identificar ventajas en términos de:
   - Escalabilidad
   - Tolerancia a fallos
   - Distribución de carga
3. Analizar los sacrificios:
   - Complejidad
   - Latencia
   - Consistencia fuerte
4. Evaluar el impacto del churn en el sistema.

---

## 📚 Entregables

- Documento formal con desarrollo matemático y argumentativo.
- Tablas correctamente construidas.
- Rutas de lookup justificadas paso a paso.
- Análisis crítico arquitectónico.
- Video explicativo breve (5–10 minutos) demostrando dominio conceptual.

---

## 📖 Referencia Base

- Stoica, I., et al. (2001). *Chord: A Scalable Peer-to-Peer Lookup Protocol*. ACM SIGCOMM.
