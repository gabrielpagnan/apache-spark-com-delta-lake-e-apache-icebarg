# Apache Spark com Delta Lake e Apache Iceberg

> **Trabalho de Arquitetura de Dados — SATC**  
> Disciplina: Arquitetura de Dados  
> Tema: Apache Spark com Delta Lake e Apache Iceberg

## Participantes 

| Nome | 
|------|
| Gabriel Matiola | 
| Pedro Afonso Cardoso Barato | 
| Luan | 

---

## Visão Geral

Este projeto demonstra o ecossistema **Data Lakehouse** usando:

- **Apache Spark 3.5** — motor de processamento distribuído
- **Delta Lake 3.2** — formato de tabela ACID open-source (Databricks / Linux Foundation)
- **Apache Iceberg 1.6.1** — formato de tabela ACID open-source (Netflix / Apache Foundation)
- **UV** — gerenciador de projetos e pacotes Python
- **JupyterLab** — ambiente interativo de notebooks
- **MKDocs Material** — documentação web

O cenário de dados simula uma loja virtual (**TechStore**) com tabelas de clientes, produtos, pedidos e itens de pedido.

---

## Documentação

A documentação completa do projeto está disponível em:

**[https://gabrielpagnan.github.io/apache-spark-com-delta-lake-e-apache-icebarg](https://gabrielpagnan.github.io/apache-spark-com-delta-lake-e-apache-icebarg)**

---

## Pré-requisitos

Antes de reproduzir o ambiente, certifique-se de ter instalado:

### 1. Python 3.11

Baixe e instale o Python 3.11 em [python.org/downloads](https://www.python.org/downloads/).

Verifique a instalação:

```bash
python --version
# Python 3.11.x
```

### 2. Java 11 ou 17 (obrigatório para o Spark)

O Apache Spark requer Java na versão 11 ou 17. **Java 21+ não é compatível com Spark 3.5.**

**Windows:**
1. Baixe o JDK 17 em [adoptium.net](https://adoptium.net/)
2. Execute o instalador `.msi`
3. Configure a variável de ambiente `JAVA_HOME`:
   - Abra "Variáveis de Ambiente do Sistema"
   - Em "Variáveis do sistema", clique em "Nova"
   - Nome: `JAVA_HOME`
   - Valor: `C:\Program Files\Eclipse Adoptium\jdk-17.x.x.x-hotspot` (ajuste conforme instalação)
4. Adicione `%JAVA_HOME%\bin` ao `Path`

Verifique a instalação:

```bash
java -version
# openjdk version "17.x.x" ...
```

### 3. UV — Gerenciador de Pacotes Python

Instale o UV seguindo a [documentação oficial](https://docs.astral.sh/uv/getting-started/installation/):

**Windows (PowerShell):**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**macOS/Linux:**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Verifique a instalação:

```bash
uv --version
# uv x.x.x
```


---

## Reprodução do Ambiente

### Passo 1 — Clonar o repositório

```bash
git clone https://github.com/gabrielpagnan/apache-spark-com-delta-lake-e-apache-icebarg.git
cd apache-spark-com-delta-lake-e-apache-icebarg
```

### Passo 2 — Instalar dependências com UV

O UV lerá o arquivo `pyproject.toml` e instalará todas as dependências automaticamente em um ambiente virtual isolado.

```bash
uv sync
```

Este comando irá:
- Criar um ambiente virtual em `.venv/`
- Instalar o Python 3.11 (se necessário)
- Instalar todas as dependências listadas no `pyproject.toml`

> **Dependências instaladas:**
> - `pyspark==3.5.3`
> - `delta-spark==3.2.0`
> - `jupyterlab>=4.3.4`
> - `mkdocs>=1.6.1`
> - `mkdocs-material>=9.5.47`
> - `mkdocs-mermaid2-plugin>=1.1.1`
> - `pandas>=2.2.3`
> - `pyarrow>=18.0.0`
> - `faker>=33.0.0`

### Passo 3 — Iniciar o JupyterLab

```bash
uv run jupyter lab
```

O JupyterLab abrirá automaticamente no navegador em `http://localhost:8888`.

> **Alternativa — VS Code:** Abra a pasta do projeto no VS Code. Instale as extensões "Python" e "Jupyter". Selecione o interpretador `.venv/Scripts/python.exe` (Windows) ou `.venv/bin/python` (Linux/Mac) como kernel.

### Passo 4 — Executar os Notebooks

Na interface do JupyterLab, navegue até a pasta `notebooks/` e abra os notebooks na seguinte ordem:

#### 4.1 — Delta Lake

```
notebooks/delta_lake.ipynb
```

Execute todas as células com `Shift + Enter` ou pelo menu `Run > Run All Cells`.

> **O que acontece na primeira execução:**
> - O `configure_spark_with_delta_pip` baixa automaticamente os JARs do Delta Lake do Maven Central (~30MB).
> - Isso pode levar 2–5 minutos dependendo da sua conexão.

#### 4.2 — Apache Iceberg

```
notebooks/iceberg.ipynb
```

Execute todas as células com `Shift + Enter` ou pelo menu `Run > Run All Cells`.

> **O que acontece na primeira execução:**
> - O Spark baixa automaticamente o JAR `iceberg-spark-runtime-3.5_2.12-1.6.1.jar` do Maven Central (~50MB).
> - Isso pode levar 3–7 minutos dependendo da sua conexão.
> - O JAR fica em cache em `~/.ivy2/cache/` para execuções futuras.

---

## Solução de Problemas Comuns

### Erro: `JAVA_HOME not set` ou `Java not found`

```
Error: JAVA_HOME is not set and Java could not be found in your PATH
```

**Solução:** Instale o JDK 17 e configure a variável `JAVA_HOME` conforme o Passo 2 dos pré-requisitos.

### Erro: `WinUtils` (apenas Windows)

O Spark no Windows pode reclamar da ausência do Hadoop WinUtils. Se isso ocorrer:

1. Baixe o `winutils.exe` para Hadoop 3.3 em: [github.com/kontext-tech/winutils](https://github.com/kontext-tech/winutils)
2. Coloque o arquivo em `C:\hadoop\bin\winutils.exe`
3. Adicione a variável de ambiente:
   - Nome: `HADOOP_HOME`
   - Valor: `C:\hadoop`

### Erro: `Port 4040 already in use`

Se outra sessão Spark estiver rodando, a UI ficará em outra porta (4041, 4042...). Para liberar a porta, encerre sessões Spark anteriores ou reinicie o kernel do Jupyter.

### Erro: Download do JAR falha (proxy/firewall)

Se estiver em ambiente corporativo com proxy:

```bash
uv run pyspark --conf spark.driver.extraJavaOptions="-Dhttps.proxyHost=SEU_PROXY -Dhttps.proxyPort=PORTA"
```

### Delta Lake: `AnalysisException: Failed to find data source: delta`

Certifique-se de que está usando `configure_spark_with_delta_pip(builder)` e **não** `builder.getOrCreate()` diretamente.

---

## Estrutura do Projeto

```
spark-lakehouse/
├── .python-version          # Versão do Python gerenciada pelo UV (3.11)
├── pyproject.toml           # Dependências e metadados do projeto (UV)
├── uv.lock                  # Lock file gerado pelo UV (não editar manualmente)
├── README.md                # Este arquivo
├── mkdocs.yml               # Configuração do site de documentação
├── notebooks/
│   ├── delta_lake.ipynb     # Notebook: Delta Lake com PySpark (INSERT/UPDATE/DELETE/MERGE)
│   └── iceberg.ipynb        # Notebook: Apache Iceberg com PySpark (INSERT/UPDATE/DELETE/MERGE)
├── docs/
│   ├── index.md             # Contextualização: cenário, ER, DDL, fonte de dados
│   ├── spark.md             # Apache Spark / PySpark — conceitos e exemplos
│   ├── delta.md             # Delta Lake — arquitetura e operações
│   └── iceberg.md           # Apache Iceberg — arquitetura e operações
└── warehouse/               # Criado automaticamente ao executar os notebooks
    ├── delta/               # Tabelas Delta Lake (Parquet + _delta_log/)
    └── iceberg/             # Tabelas Iceberg (Parquet + metadados Iceberg)
```

---

## Documentação MKDocs

### Visualizar localmente

```bash
uv run mkdocs serve
```

Acesse em `http://127.0.0.1:8000`.

### Publicar no GitHub Pages

```bash
uv run mkdocs gh-deploy
```

Este comando irá:
1. Compilar o site estático
2. Fazer push para a branch `gh-pages` do repositório
3. Disponibilizar em `https://bielmpmat99.github.io/spark-lakehouse`

> **Pré-requisito:** O repositório deve ser público e o GitHub Pages deve estar habilitado (Settings > Pages > Source: `gh-pages`).

---

## Versões Utilizadas

| Componente | Versão |
|-----------|--------|
| Python | 3.11 |
| UV | ≥ 0.5 |
| Apache Spark | 3.5.3 |
| PySpark | 3.5.3 |
| Delta Lake (delta-spark) | 3.2.0 |
| Apache Iceberg JAR | 1.6.1 |
| JupyterLab | ≥ 4.3 |
| MKDocs | ≥ 1.6.1 |
| MKDocs Material | ≥ 9.5.47 |
| Faker | ≥ 33.0 |
| Pandas | ≥ 2.2 |
| PyArrow | ≥ 18.0 |
| Java | 11 ou 17 |

---

## Referências

- [Apache Spark Documentation](https://spark.apache.org/docs/3.5.3/)
- [Delta Lake Documentation](https://docs.delta.io/latest/)
- [Apache Iceberg Documentation](https://iceberg.apache.org/docs/latest/)
- [UV Documentation](https://docs.astral.sh/uv/)
- [Canal DataWay BR (YouTube)](https://www.youtube.com/@DataWayBR)
- [spark-delta (GitHub)](https://github.com/jlsilva01/spark-delta)
- [spark-iceberg (GitHub)](https://github.com/jlsilva01/spark-iceberg)
