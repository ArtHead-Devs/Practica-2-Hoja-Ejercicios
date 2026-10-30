# Práctica 2: Hoja de Ejercicios 1

Resolución de la Hoja de Ejercicios 1 de la asignatura **Bioinformática en laboratorio** (T02). Los ejercicios se resuelven en un único cuaderno de Jupyter con [Biopython](https://biopython.org/) y trabajan con datos reales descargados del NCBI y de UniProt.

## Autores

- Pablo Cabeza Lantigua
- Jose Carlos Rodríguez Rodríguez

## Contenido de los ejercicios

| Ejercicio | Tema | Qué hace |
|---|---|---|
| 1 | Porcentaje GC | Lee un FASTA con varias secuencias de ADN, calcula el porcentaje de GC de cada una, las traduce a proteína y escribe un FASTA de proteínas con los identificadores y descripciones originales. |
| 2 | El sarampión | Busca con `Entrez.esearch` el genoma de RefSeq del virus del sarampión, descarga el registro FASTA con `Entrez.efetch` y muestra su descripción y los 100 primeros nucleótidos. |
| 3 | La COVID-19 | Descarga en formato GenBank el genoma de referencia del SARS-CoV-2 (`NC_045512`), filtra las características de tipo `gene` y lista nombre y coordenadas de cada uno. |
| 4 | Proteínas de resistencia | Descarga el plásmido pT181 de *Staphylococcus aureus*, localiza sus regiones CDS y las traduce a proteínas. |
| 5 | Proteínas célebres | Descarga los FASTA de tres proteínas humanas (mioglobina, albúmina y prolactina) desde UniProt y resume su función. |

## Estructura del repositorio

```
.
├── ejercicios.ipynb      Cuaderno con la resolución de todos los ejercicios
├── data/                 Ficheros de entrada, descargados y generados
│   ├── ejercicio1/       FASTA de ADN (entrada) y FASTA de proteínas (salida)
│   ├── ejercicio2/       Genoma del virus del sarampión (FASTA)
│   ├── ejercicio3/       Genoma del SARS-CoV-2 (GenBank)
│   ├── ejercicio4/       Plásmido pT181 (GenBank)
│   └── ejercicio5/       FASTA de las tres proteínas elegidas
├── pyproject.toml        Dependencias del proyecto
├── uv.lock               Versiones fijadas de las dependencias
└── LICENSE
```

Los registros descargados con `Entrez.efetch` se guardan en `data/` para poder trabajar sin conexión si el NCBI no está disponible.

## Requisitos

- Python 3.12 o superior.
- [uv](https://docs.astral.sh/uv/) como gestor de entornos y dependencias.
- Conexión a internet para ejecutar las descargas del NCBI.
- Dependencias, definidas en `pyproject.toml`: `biopython` y `python-dotenv`. Para el cuaderno también se usa `ipykernel`, incluido en el grupo de desarrollo.

## Instalación y ejecución

1. Clona el repositorio y entra en la carpeta del proyecto.
2. Instala las dependencias:

   ```bash
   uv sync
   ```

3. Crea un fichero `.env` en la raíz del proyecto con tu correo. El NCBI exige identificarse en cada consulta de Entrez, y el cuaderno lee la variable `ENTREZ_EMAIL` una sola vez al principio. El fichero está en `.gitignore`, así que no se sube al repositorio.

   ```
   ENTREZ_EMAIL=tu_correo@ejemplo.com
   ```

4. Abre `ejercicios.ipynb` en VS Code o en Jupyter, selecciona el kernel del entorno `.venv` y ejecuta las celdas en orden.

## Decisiones y notas por ejercicio

### Ejercicio 1

El enunciado no incluye fichero de entrada. Se descargó desde la web del NCBI el FASTA con las secuencias codificantes (CDS) del plásmido ColE1 de *Escherichia coli* (`NC_001371`) y se guardó como `data/ejercicio1/secuencias_adn.fasta`. Esta elección fue confirmada por el profesor.

Las secuencias se traducen con `translate(table=11, cds=True)`. La tabla 11 es el código genético bacteriano, y `cds=True` comprueba que la región codificante está completa, convierte el codón de inicio alternativo en metionina y elimina el codón de parada.

### Ejercicio 2

La instrucción pide usar la base de datos secundaria. Se interpreta como RefSeq, y se aplica con el filtro `srcdb_refseq[PROP]` en la búsqueda. El resultado es el registro `NC_001498.1`.

### Ejercicio 3

Biopython cuenta las posiciones desde 0. Al mostrar el inicio de cada gen se suma 1 para que las coordenadas coincidan con las de GenBank. El genoma de referencia contiene 11 genes.

### Ejercicio 4

- **Código de acceso.** El enunciado indica `NC_001371`, pero ese código corresponde al plásmido ColE1 de *E. coli*. El plásmido pT181 de *S. aureus* es `NC_001393`, que es el que se usa. El profesor confirmó que esta era la intención original del ejercicio.
- **Formato de descarga.** `NC_001393` es un registro de tipo CON, y con `rettype="gb"` llega sin la secuencia. Se usa `rettype="gbwithparts"`, que sigue siendo formato GenBank e incluye la secuencia completa.
- **Gen de resistencia Tet(K).** El plásmido tiene tres CDS. Dos se traducen con `cds=True`. El tercero, la bomba de expulsión de tetraciclina Tet(K), está marcado en RefSeq como pseudogén por un desplazamiento del marco de lectura. Al ser la proteína de resistencia del plásmido, se traduce igualmente sin `cds=True`. El cuaderno muestra un aviso, porque la traducción contiene codones de parada internos.

### Ejercicio 5

Las proteínas elegidas son humanas y se identifican por su código de UniProt:

| Proteína | Gen | Código |
|---|---|---|
| Mioglobina | `MB` | `P02144` |
| Albúmina | `ALB` | `P02768` |
| Prolactina | `PRL` | `P01236` |

Los FASTA se descargan de la base de datos `protein` con `Entrez.efetch`. El cuaderno incluye una tabla con la ubicación, la función y un dato concreto de cada proteína, con enlaces a sus fichas de UniProt.

## Licencia

Consulta el fichero [LICENSE](LICENSE).
