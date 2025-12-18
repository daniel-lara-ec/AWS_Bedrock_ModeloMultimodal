# AWS Bedrock - Modelo Multimodal

Proyecto de prueba de concepto (POC) para procesamiento de documentos PDF utilizando **AWS Bedrock** con modelos multimodales, específicamente el modelo **Amazon Nova Pro v1**.

## 📋 Descripción

Este proyecto implementa soluciones para:

1. **OCR Multimodal**: Extracción de texto de imágenes y documentos PDF usando modelos de visión de AWS Bedrock
2. **Procesamiento por Lotes**: Integración con el servicio de Batch de Bedrock para procesar múltiples PDFs de forma escalable
3. **Conversión de Formatos**: Transformación de PDFs a imágenes para procesamiento mediante IA
4. **Extracción de Fórmulas**: Reconocimiento y conversión a formato LaTeX de contenido matemático

## 🛠️ Requisitos

- Python 3.8+
- Credenciales de AWS configuradas (con acceso a Bedrock)
- Bucket S3 para almacenamiento temporal (para procesamiento en lotes)

### Dependencias Python

```
boto3>=1.26.0
pillow>=9.0.0
pymupdf>=1.23.0
pdf2image>=1.16.0
numpy>=1.21.0
tqdm>=4.64.0
python-dotenv>=0.20.0
```

## 📦 Instalación

1. **Clona el repositorio:**

   ```bash
   git clone <repo-url>
   cd AWS_Bedrock_ModeloMultimodal
   ```

2. **Crea un entorno virtual:**

   ```bash
   python -m venv venv
   venv\Scripts\activate
   ```

3. **Instala las dependencias:**

   ```bash
   pip install -r requirements.txt
   ```

4. **Configura las credenciales de AWS:**
   - Asegúrate de tener configuradas tus credenciales en `~/.aws/credentials` o variables de entorno
   - Para procesamiento en lotes, crea un archivo `.env` con:
     ```
     AWS_BUCKET=tu-bucket-s3
     AWS_ROLE_ARN=arn:aws:iam::ACCOUNT_ID:role/bedrock-batch-role
     ```

## 📁 Estructura del Proyecto

```
├── README.md                  # Este archivo
├── requirements.txt           # Dependencias Python
├── notebooks/
│   ├── 01-POC.ipynb          # POC básico: procesamiento individual
│   └── 02-POC_Batch.ipynb    # POC avanzado: procesamiento en lotes
├── code/
│   └── batch_input.jsonl     # Ejemplo de entrada para Batch API
├── data/
│   └── documents/            # Ubicación para archivos PDF de entrada
├── salida/                   # Resultados de procesamiento
└── tmp.json                  # Archivo temporal
```

## 🚀 Uso

### Notebook 1: Procesamiento Individual (`01-POC.ipynb`)

Demuestra cómo procesar documentos individuales de forma síncrona:

```python
# Inicializar cliente Bedrock
client = aws_bedrock_client()

# Procesar imagen con OCR
resultado = ocr_image_with_nova(
    client,
    image_path=Path("documento.png"),
    prompt="Extrae todo el texto visible de la página"
)

# Resultado contiene:
# - texto_salida: Texto extraído
# - tokens_entrada: Tokens consumidos (input)
# - tokens_salida: Tokens consumidos (output)
# - tiempo_procesamiento: Latencia
```

### Notebook 2: Procesamiento en Lotes (`02-POC_Batch.ipynb`)

Demuestra procesamiento escalable usando AWS Bedrock Batch API:

```python
# Generar entrada JSONL para Batch
generate_batch_jsonl_nova_images_s3(
    pdf_path="documento.pdf",
    output_jsonl="batch_input.jsonl",
    bucket="mi-bucket",
    prefix="documentos",
    account_id="123456789012",
    dpi=200
)

# Procesar lote mediante AWS Bedrock Batch API
# Resultados almacenados en S3 y manifest.json
```

## 🔧 Configuración

Parámetros principales en los notebooks:

| Parámetro           | Descripción                             | Valor por defecto      |
| ------------------- | --------------------------------------- | ---------------------- |
| `REGION`            | Región AWS                              | `us-east-1`            |
| `MODEL_ID`          | Modelo Bedrock a usar                   | `amazon.nova-pro-v1:0` |
| `MIN_CHARS_DIGITAL` | Umbral para detectar PDFs digitalizados | `20`                   |
| `DPI`               | Resolución de conversión PDF→imagen     | `400`                  |

## 💡 Casos de Uso

- 📄 Digitalización de documentos escaneados
- 📊 Extracción de datos de tablas y formularios
- 🧮 Reconocimiento de fórmulas matemáticas (salida en LaTeX)
- 📚 Procesamiento masivo de archivos en lotes
- 🔍 Análisis de contenido visual en documentos

## ⚠️ Consideraciones Importantes

1. **Costos**: El modelo Nova Pro cobra por tokens. Monitorea el uso en cada ejecución
2. **Límites de concurrencia**: Bedrock tiene límites de TPS. Para lotes grandes, usa Batch API
3. **Resolución**: Una mayor resolución (DPI) mejora precisión pero aumenta costos
4. **Tipo de documento**: El OCR funciona mejor con documentos de buena calidad

---

**Última actualización**: 17 de diciembre de 2025
