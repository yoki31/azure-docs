---
title: Azure Synapse Runtime for Apache Spark 3.1  
description: Supported versions of Spark, Scala, Python, and .NET for Apache Spark 3.1.
services: synapse-analytics
author: midesa 
ms.service: synapse-analytics 
ms.topic: reference
ms.subservice: spark
ms.date: 09/22/2021 
ms.author: midesa 
ms.custom: has-adal-ref
---

# Azure Synapse Runtime for Apache Spark 3.1 

Azure Synapse Analytics supports multiple runtimes for Apache Spark. This document will cover the runtime components and versions for the Azure Synapse Runtime for Apache Spark 3.1. 

## Known Issues
* Synapse Pipeline/Dataflows support is coming soon.
* The following connector support are coming soon:
  * Azure Data Explorer connector
  * SQL Server
* Hyperspace, Spark Cruise, and Dynamic Allocation Executors are coming soon.

## Component versions
|  Component   | Version   |  
| ----- | ----- |
| Apache Spark | 3.1 |
| Operating System | Ubuntu 18.04 |
| Java | 1.8.0_282 |
| Scala | 2.12.10  |
| Hadoop | 3.1.1  |
| .NET Core | 3.1 |
| .NET | 2.0.0 |
| Delta Lake | 1.0 |
| Python | 3.8 |

## Scala and Java libraries

HikariCP-2.5.1.jar

JLargeArrays-1.5.jar

JTransforms-3.1.jar

RoaringBitmap-0.9.0.jar

ST4-4.0.4.jar

SparkCustomEvents_3.1.2-1.0.0.jar

TokenLibrary-assembly-1.0.jar

VegasConnector-1.0.25.1_2.12.jar

accessors-smart-1.2.jar

activation-1.1.1.jar

adal4j-1.6.3.jar

aircompressor-0.10.jar

algebra_2.12-2.0.0-M2.jar

animal-sniffer-annotations-1.17.jar

antlr-runtime-3.5.2.jar

antlr4-runtime-4.8-1.jar

aopalliance-1.0.jar

aopalliance-repackaged-2.6.1.jar

apache-log4j-extras-1.2.17.jar

arpack_combined_all-0.1.jar

arrow-format-2.0.0.jar

arrow-memory-core-2.0.0.jar

arrow-memory-netty-2.0.0.jar

arrow-vector-2.0.0.jar

avro-1.8.2.jar

avro-ipc-1.8.2.jar

avro-mapred-1.8.2-hadoop2.jar

aws-java-sdk-bundle-1.11.375.jar

azure-eventhubs-3.3.0.jar

azure-eventhubs-spark_2.12-2.3.21.jar

azure-keyvault-core-1.0.0.jar

azure-storage-7.0.1.jar

azure-synapse-ml-pandas_2.12-1.0.0.jar

azure-synapse-ml-predict_2.12-1.0.jar

bcpkix-jdk15on-1.60.jar

bcprov-jdk15on-1.60.jar

bonecp-0.8.0.RELEASE.jar

breeze-macros_2.12-1.0.jar

breeze_2.12-1.0.jar

cats-kernel_2.12-2.0.0-M4.jar

checker-qual-2.8.1.jar

chill-java-0.9.5.jar

chill_2.12-0.9.5.jar

client-sdk-1.14.0.jar

cntk-2.4.jar

commons-beanutils-1.9.4.jar

commons-cli-1.2.jar

commons-codec-1.10.jar

commons-collections-3.2.2.jar

commons-compiler-3.0.16.jar

commons-compress-1.20.jar

commons-configuration2-2.1.1.jar

commons-crypto-1.1.0.jar

commons-daemon-1.0.13.jar

commons-dbcp-1.4.jar

commons-httpclient-3.1.jar

commons-io-2.5.jar

commons-lang-2.6.jar

commons-lang3-3.10.jar

commons-logging-1.1.3.jar

commons-math3-3.4.1.jar

commons-net-3.1.jar

commons-pool-1.5.4.jar

commons-pool2-2.6.2.jar

commons-text-1.6.jar

compress-lzf-1.0.3.jar

config-1.3.4.jar

core-1.1.2.jar

cosmos-analytics-spark-connector_3-1_2-12-assembly-3.0.4.jar

cudf-21.10.0-cuda11.jar

curator-client-2.12.0.jar

curator-framework-2.12.0.jar

curator-recipes-2.12.0.jar

datanucleus-api-jdo-4.2.4.jar

datanucleus-core-4.1.6.jar

datanucleus-rdbms-4.1.19.jar

delta-core_2.12-1.0.0.2b.jar

derby-10.12.1.1.jar

dnsjava-2.1.7.jar

dropwizard-metrics-hadoop-metrics2-reporter-0.1.2.jar

ehcache-3.3.1.jar

error_prone_annotations-2.3.2.jar

failureaccess-1.0.1.jar

flatbuffers-java-1.9.0.jar

fluent-logger-jar-with-dependencies.jar

geronimo-jcache_1.0_spec-1.0-alpha-1.jar

gson-2.2.4.jar

guava-28.0-jre.jar

guice-4.0.jar

guice-servlet-4.0.jar

hadoop-annotations-3.1.1.5.0-50849917.jar

hadoop-auth-3.1.1.5.0-50849917.jar

hadoop-aws-3.1.1.5.0-50849917.jar

hadoop-azure-3.1.1.5.0-50849917.jar

hadoop-client-3.1.1.5.0-50849917.jar

hadoop-common-3.1.1.5.0-50849917.jar

hadoop-hdfs-client-3.1.1.5.0-50849917.jar

hadoop-mapreduce-client-common-3.1.1.5.0-50849917.jar

hadoop-mapreduce-client-core-3.1.1.5.0-50849917.jar

hadoop-mapreduce-client-jobclient-3.1.1.5.0-50849917.jar

hadoop-openstack-3.1.1.5.0-50849917.jar

hadoop-yarn-api-3.1.1.5.0-50849917.jar

hadoop-yarn-client-3.1.1.5.0-50849917.jar

hadoop-yarn-common-3.1.1.5.0-50849917.jar

hadoop-yarn-registry-3.1.1.5.0-50849917.jar

hadoop-yarn-server-common-3.1.1.5.0-50849917.jar

hadoop-yarn-server-web-proxy-3.1.1.5.0-50849917.jar

hdinsight-spark-metrics_3_1_2-1.0.0.jar

hive-beeline-2.3.7.jar

hive-cli-2.3.7.jar

hive-common-2.3.7.jar

hive-exec-2.3.7-core.jar

hive-jdbc-2.3.7.jar

hive-llap-common-2.3.7.jar

hive-metastore-2.3.7.jar

hive-serde-2.3.7.jar

hive-service-rpc-3.1.2.jar

hive-shims-0.23-2.3.7.jar

hive-shims-2.3.7.jar

hive-shims-common-2.3.7.jar

hive-shims-scheduler-2.3.7.jar

hive-storage-api-2.7.2.jar

hive-vector-code-gen-2.3.7.jar

hk2-api-2.6.1.jar

hk2-locator-2.6.1.jar

hk2-utils-2.6.1.jar

htrace-core4-4.1.0-incubating.jar

httpclient-4.5.6.jar

httpcore-4.4.12.jar

httpmime-4.5.6.jar

hyperspace-core-spark3.1_2.12-0.5.0-synapse.jar

isolation-forest_3.0.0_2.12-1.0.1.jar

istack-commons-runtime-3.0.8.jar

ivy-2.4.0.jar

j2objc-annotations-1.3.jar

jackson-annotations-2.10.0.jar

jackson-core-2.10.0.jar

jackson-core-asl-1.9.13.jar

jackson-databind-2.10.0.jar

jackson-dataformat-cbor-2.10.0.jar

jackson-jaxrs-base-2.10.0.jar

jackson-jaxrs-json-provider-2.10.0.jar

jackson-mapper-asl-1.9.13.jar

jackson-module-jaxb-annotations-2.10.0.jar

jackson-module-paranamer-2.10.0.jar

jackson-module-scala_2.12-2.10.0.jar

jakarta.activation-api-1.2.1.jar

jakarta.annotation-api-1.3.5.jar

jakarta.inject-2.6.1.jar

jakarta.servlet-api-4.0.3.jar

jakarta.validation-api-2.0.2.jar

jakarta.ws.rs-api-2.1.6.jar

jakarta.xml.bind-api-2.3.2.jar

janino-3.0.16.jar

javassist-3.25.0-GA.jar

javatuples-1.2.jar

javax.inject-1.jar

javax.jdo-3.2.0-m3.jar

javolution-5.5.1.jar

jaxb-api-2.2.11.jar

jaxb-runtime-2.3.2.jar

jcip-annotations-1.0-1.jar

jcl-over-slf4j-1.7.30.jar

jdo-api-3.0.1.jar

jersey-client-2.30.jar

jersey-common-2.30.jar

jersey-container-servlet-2.30.jar

jersey-container-servlet-core-2.30.jar

jersey-entity-filtering-2.30.jar

jersey-hk2-2.30.jar

jersey-media-jaxb-2.30.jar

jersey-media-json-jackson-2.30.jar

jersey-server-2.30.jar

jetty-util-9.4.40.v20210413.jar

jetty-util-ajax-9.4.40.v20210413.jar

jline-2.14.6.jar

joda-time-2.10.5.jar

jodd-core-3.5.2.jar

jpam-1.1.jar

jsch-0.1.54.jar

json-1.8.jar

json-20090211.jar

json-simple-1.1.jar

json-smart-2.3.jar

json4s-ast_2.12-3.7.0-M5.jar

json4s-core_2.12-3.7.0-M5.jar

json4s-jackson_2.12-3.7.0-M5.jar

json4s-scalap_2.12-3.7.0-M5.jar

jsp-api-2.1.jar

jsr305-3.0.0.jar

jta-1.1.jar

jul-to-slf4j-1.7.30.jar

kafka-clients-2.4.1.5.0-50849917.jar

kerb-admin-1.0.1.jar

kerb-client-1.0.1.jar

kerb-common-1.0.1.jar

kerb-core-1.0.1.jar

kerb-crypto-1.0.1.jar

kerb-identity-1.0.1.jar

kerb-server-1.0.1.jar

kerb-simplekdc-1.0.1.jar

kerb-util-1.0.1.jar

kerby-asn1-1.0.1.jar

kerby-config-1.0.1.jar

kerby-pkix-1.0.1.jar

kerby-util-1.0.1.jar

kerby-xdr-1.0.1.jar

kryo-shaded-4.0.2.jar

kusto-data-2.7.0.jar

kusto-ingest-2.7.0.jar

kusto-spark_3.0_2.12-2.7.5.jar

leveldbjni-all-1.8.jar

libfb303-0.9.3.jar

libthrift-0.12.0.jar

libvegasjni.so

lightgbmlib-3.2.110.jar

listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar

log4j-1.2.17.jar

lz4-java-1.7.1.jar

machinist_2.12-0.6.8.jar

macro-compat_2.12-1.1.1.jar

mdsdclientdynamic-2.0.jar

metrics-core-4.1.1.jar

metrics-graphite-4.1.1.jar

metrics-jmx-4.1.1.jar

metrics-json-4.1.1.jar

metrics-jvm-4.1.1.jar

microsoft-catalog-metastore-client-1.0.57.jar

microsoft-log4j-etwappender-1.0.jar

microsoft-spark.jar

minlog-1.3.0.jar

mmlspark-1.0.0-rc3-179-327be83c-SNAPSHOT.jar

mmlspark-cognitive-1.0.0-rc3-179-327be83c-SNAPSHOT.jar

mmlspark-core-1.0.0-rc3-179-327be83c-SNAPSHOT.jar

mmlspark-deep-learning-1.0.0-rc3-179-327be83c-SNAPSHOT.jar

mmlspark-lightgbm-1.0.0-rc3-179-327be83c-SNAPSHOT.jar

mmlspark-opencv-1.0.0-rc3-179-327be83c-SNAPSHOT.jar

mmlspark-vw-1.0.0-rc3-179-327be83c-SNAPSHOT.jar

mssql-jdbc-8.4.1.jre8.jar

mysql-connector-java-8.0.18.jar

netty-all-4.1.51.Final.jar

nimbus-jose-jwt-4.41.1.jar

notebook-utils-3.0.0-20211110.6.jar

objenesis-2.6.jar

okhttp-2.7.5.jar

okio-1.14.0.jar

onnxruntime_gpu-1.8.1.jar

opencsv-2.3.jar

opencv-3.2.0-1.jar

orc-core-1.5.12.jar

orc-mapreduce-1.5.12.jar

orc-shims-1.5.12.jar

oro-2.0.8.jar

osgi-resource-locator-1.0.3.jar

paranamer-2.8.jar

parquet-column-1.10.1.jar

parquet-common-1.10.1.jar

parquet-encoding-1.10.1.jar

parquet-format-2.4.0.jar

parquet-hadoop-1.10.1.jar

parquet-jackson-1.10.1.jar

peregrine-spark-0.9.jar

postgresql-42.2.9.jar

protobuf-java-2.5.0.jar

proton-j-0.33.8.jar

py4j-0.10.9.jar

pyrolite-4.30.jar

qpid-proton-j-extensions-1.2.4.jar

rapids-4-spark_2.12-21.10.0.jar

re2j-1.1.jar

scala-collection-compat_2.12-2.1.1.jar

scala-compiler-2.12.10.jar

scala-java8-compat_2.12-0.9.0.jar

scala-library-2.12.10.jar

scala-parser-combinators_2.12-1.1.2.jar

scala-reflect-2.12.10.jar

scala-xml_2.12-1.2.0.jar

scalactic_2.12-3.0.5.jar

shapeless_2.12-2.3.3.jar

shims-0.9.0.jar

slf4j-api-1.7.30.jar

slf4j-log4j12-1.7.16.jar

snappy-java-1.1.8.2.jar

spark-3.1-rpc-history-server-app-listener_2.12-1.0.0.jar

spark-3.1-rpc-history-server-core_2.12-1.0.0.jar

spark-avro_2.12-3.1.2.5.0-50849917.jar

spark-catalyst_2.12-3.1.2.5.0-50849917.jar

spark-cdm-connector-assembly-1.19.2.jar

spark-core_2.12-3.1.2.5.0-50849917.jar

spark-enhancement_2.12-3.1.2.5.0-50849917.jar

spark-enhancementui_2.12-1.1.0.jar

spark-graphx_2.12-3.1.2.5.0-50849917.jar

spark-hadoop-cloud_2.12-3.1.2.5.0-50849917.jar

spark-hive-thriftserver_2.12-3.1.2.5.0-50849917.jar

spark-hive_2.12-3.1.2.5.0-50849917.jar

spark-kusto-synapse-connector_3.1_2.12-1.0.0.jar

spark-kvstore_2.12-3.1.2.5.0-50849917.jar

spark-launcher_2.12-3.1.2.5.0-50849917.jar

spark-microsoft-telemetry_2.12-3.1.2.5.0-50849917.jar

spark-microsoft-tools_2.12-3.1.2.5.0-50849917.jar

spark-mllib-local_2.12-3.1.2.5.0-50849917.jar

spark-mllib_2.12-3.1.2.5.0-50849917.jar

spark-mssql-connector-1.2.0.jar

spark-network-common_2.12-3.1.2.5.0-50849917.jar

spark-network-shuffle_2.12-3.1.2.5.0-50849917.jar

spark-repl_2.12-3.1.2.5.0-50849917.jar

spark-sketch_2.12-3.1.2.5.0-50849917.jar

spark-sql-kafka-0-10_2.12-3.1.2.5.0-50849917.jar

spark-sql_2.12-3.1.2.5.0-50849917.jar

spark-streaming-kafka-0-10-assembly_2.12-3.1.2.5.0-50849917.jar

spark-streaming-kafka-0-10_2.12-3.1.2.5.0-50849917.jar

spark-streaming_2.12-3.1.2.5.0-50849917.jar

spark-tags_2.12-3.1.2.5.0-50849917.jar

spark-token-provider-kafka-0-10_2.12-3.1.2.5.0-50849917.jar

spark-unsafe_2.12-3.1.2.5.0-50849917.jar

spark-yarn_2.12-3.1.2.5.0-50849917.jar

spark_diagnostic_cli-1.0.10_spark-3.1.2.jar

spire-macros_2.12-0.17.0-M1.jar

spire-platform_2.12-0.17.0-M1.jar

spire-util_2.12-0.17.0-M1.jar

spire_2.12-0.17.0-M1.jar

spray-json_2.12-1.3.2.jar

sqlanalyticsconnector_3.1.2-1.0.1.jar

stax-api-1.0.1.jar

stax2-api-3.1.4.jar

stream-2.9.6.jar

structuredstreamforspark_2.12-3.0.1-2.1.3.jar

super-csv-2.2.0.jar

synapse-spark-telemetry_2.12-0.0.6.jar

synfs-3.0.0-20211110.6.jar

threeten-extra-1.5.0.jar

token-provider-1.0.1.jar

transaction-api-1.1.jar

univocity-parsers-2.9.1.jar

velocity-1.5.jar

vw-jni-8.9.1.jar

wildfly-openssl-1.0.7.Final.jar

woodstox-core-5.0.3.jar

xbean-asm7-shaded-4.15.jar

xz-1.5.jar

zookeeper-3.4.6.5.0-50849917.jar

zstd-jni-1.4.8-1.jar


## Python libraries 

_openmp_mutex=4.5

_py-xgboost-mutex=2.0

abseil-cpp=20210324.0

absl-py=0.13.0

adal=1.2.7

adlfs=0.7.7

aiohttp=3.7.4.post0

alsa-lib=1.2.3

appdirs=1.4.4

arrow-cpp=3.0.0

astor=0.8.1

astunparse=1.6.3

async-timeout=3.0.1

attrs=21.2.0

azure-datalake-store=0.0.51

azure-identity=2021.03.15b1

azure-storage-blob=12.8.1

backcall=0.2.0

backports=1.0

backports.functools_lru_cache=1.6.4

beautifulsoup4=4.9.3

blas=2.109

blas-devel=3.9.0

blinker=1.4

blosc=1.21.0

bokeh=2.3.2

brotli=1.0.9

brotli-bin=1.0.9

brotli-python=1.0.9

brotlipy=0.7.0

brunsli=0.1

bzip2=1.0.8

c-ares=1.17.1

ca-certificates=2021.7.5

cachetools=4.2.2

cairo=1.16.0

certifi=2021.5.30

cffi=1.14.5

chardet=4.0.0

charls=2.2.0

click=8.0.1

cloudpickle=1.6.0

conda=4.9.2

conda-package-handling=1.7.3

configparser=5.0.2

cryptography=3.4.7

cudatoolkit=11.1.1

cycler=0.10.0

cython=0.29.23

cytoolz=0.11.0

dash=1.20.0

dash-core-components=1.16.0

dash-html-components=1.1.3

dash-renderer=1.9.1

dash-table=4.11.3

dash_cytoscape=0.2.0

dask-core=2021.6.2

databricks-cli=0.12.1

dataclasses=0.8

dbus=1.13.18

debugpy=1.3.0

decorator=4.4.2

dill=0.3.4

entrypoints=0.3

et_xmlfile=1.1.0

expat=2.4.1

fire=0.4.0

flask=2.0.1

flask-compress=1.10.1

fontconfig=2.13.1

freetype=2.10.4

fsspec=2021.6.1

future=0.18.2

gast=0.3.3

gensim=3.8.3

geographiclib=1.52

geopy=2.1.0

gettext=0.21.0

gevent=21.1.2

gflags=2.2.2

giflib=5.2.1

gitdb=4.0.7

gitpython=3.1.18

glib=2.68.3

glib-tools=2.68.3

glog=0.5.0

gobject-introspection=1.68.0

google-auth=1.32.1

google-auth-oauthlib=0.4.1

google-pasta=0.2.0

greenlet=1.1.0

grpc-cpp=1.37.1

grpcio=1.37.1

gst-plugins-base=1.18.4

gstreamer=1.18.4

h5py=2.10.0

hdf5=1.10.6

html5lib=1.1

hummingbird-ml=0.4.0

icu=68.1

idna=2.10

imagecodecs=2021.3.31

imageio=2.9.0

importlib-metadata=4.6.1

intel-openmp=2021.2.0

interpret=0.2.4

interpret-core=0.2.4

ipykernel=6.0.1

ipython=7.23.1

ipython_genutils=0.2.0

isodate=0.6.0

itsdangerous=2.0.1

jdcal=1.4.1

jedi=0.18.0

jinja2=3.0.1

joblib=1.0.1

jpeg=9d

jupyter_client=6.1.12

jupyter_core=4.7.1

jxrlib=1.1

keras-applications=1.0.8

keras-preprocessing=1.1.2

keras2onnx=1.6.5

kiwisolver=1.3.1

koalas=1.8.0

krb5=1.19.1

lcms2=2.12

ld_impl_linux-64=2.36.1

lerc=2.2.1

liac-arff=2.5.0

libaec=1.0.5

libblas=3.9.0

libbrotlicommon=1.0.9

libbrotlidec=1.0.9

libbrotlienc=1.0.9

libcblas=3.9.0

libclang=11.1.0

libcurl=7.77.0

libdeflate=1.7

libedit=3.1.20210216

libev=4.33

libevent=2.1.10

libffi=3.3

libgcc-ng=9.3.0

libgfortran-ng=9.3.0

libgfortran5=9.3.0

libglib=2.68.3

libiconv=1.16

liblapack=3.9.0

liblapacke=3.9.0

libllvm10=10.0.1

libllvm11=11.1.0

libnghttp2=1.43.0

libogg=1.3.5

libopus=1.3.1

libpng=1.6.37

libpq=13.3

libprotobuf=3.15.8

libsodium=1.0.18

libssh2=1.9.0

libstdcxx-ng=9.3.0

libthrift=0.14.1

libtiff=4.2.0

libutf8proc=2.6.1

libuuid=2.32.1

libuv=1.41.1

libvorbis=1.3.7

libwebp-base=1.2.0

libxcb=1.14

libxgboost=1.4.0

libxkbcommon=1.0.3

libxml2=2.9.12

libzopfli=1.0.3

lightgbm=3.2.1

lime=0.2.0.1

llvm-openmp=11.1.0

llvmlite=0.36.0

locket=0.2.1

lz4-c=1.9.3

markdown=3.3.4

markupsafe=2.0.1

matplotlib=3.4.2

matplotlib-base=3.4.2

matplotlib-inline=0.1.2

mkl=2021.2.0

mkl-devel=2021.2.0

mkl-include=2021.2.0

mleap=0.17.0

mlflow-skinny=1.18.0

msal=2021.06.08

msal-extensions=2021.06.08

msrest=2021.06.01

multidict=5.1.0

mysql-common=8.0.25

mysql-libs=8.0.25

ncurses=6.2

networkx=2.5.1

ninja=1.10.2

nltk=3.6.2

nspr=4.30

nss=3.67

numba=0.53.1

numpy=1.19.4

oauthlib=3.1.1

olefile=0.46

onnx=1.9.0

onnxconverter-common=1.7.0

onnxmltools=1.7.0

onnxruntime=1.7.2

openjpeg=2.4.0

openpyxl=3.0.7

openssl=1.1.1k

opt_einsum=3.3.0

orc=1.6.7

packaging=21.0

pandas=1.2.3

parquet-cpp=1.5.1

parso=0.8.2

partd=1.2.0

patsy=0.5.1

pcre=8.45

pexpect=4.8.0

pickleshare=0.7.5

pillow=8.2.0

pip=21.1.1

pixman=0.40.0

plotly=4.14.3

pmdarima=1.8.2

pooch=1.4.0

portalocker=1.7.1

prompt-toolkit=3.0.19

protobuf=3.15.8

psutil=5.8.0

ptyprocess=0.7.0

py-xgboost=1.4.0

py4j=0.10.9

pyarrow=3.0.0

pyasn1=0.4.8

pyasn1-modules=0.2.8

pycairo=1.20.1

pycosat=0.6.3

pycparser=2.20

pygments=2.9.0

pygobject=3.40.1

pyjwt=2.1.0

pyodbc=4.0.30

pyopenssl=20.0.1

pyparsing=2.4.7

pyqt=5.12.3

pyqt-impl=5.12.3

pyqt5-sip=4.19.18

pyqtchart=5.12

pyqtwebengine=5.12.1

pysocks=1.7.1

pyspark=3.1.2

python=3.8.10

python-dateutil=2.8.1

python-flatbuffers=1.12

python_abi=3.8

pytorch=1.8.1

pytz=2021.1

pyu2f=0.1.5

pywavelets=1.1.1

pyyaml=5.4.1

pyzmq=22.1.0

qt=5.12.9

re2=2021.04.01

readline=8.1

regex=2021.7.6

requests=2.25.1

requests-oauthlib=1.3.0

retrying=1.3.3

rsa=4.7.2

ruamel_yaml=0.15.100

s2n=1.0.10

salib=1.3.11

scikit-image=0.18.1

scikit-learn=0.23.2

scipy=1.5.3

seaborn=0.11.1

seaborn-base=0.11.1

setuptools=49.6.0

shap=0.39.0

six=1.16.0

skl2onnx=1.8.0.1

sklearn-pandas=2.2.0

slicer=0.0.7

smart_open=5.1.0

smmap=3.0.5

snappy=1.1.8

soupsieve=2.2.1

sqlite=3.36.0

statsmodels=0.12.2

tabulate=0.8.9

tenacity=7.0.0

tensorboard=2.4.1

tensorboard-plugin-wit=1.8.0

tensorflow=2.4.1

tensorflow-base=2.4.1

tensorflow-estimator=2.4.0

termcolor=1.1.0

textblob=0.15.3

threadpoolctl=2.1.0

tifffile=2021.4.8

tk=8.6.10

toolz=0.11.1

tornado=6.1

tqdm=4.61.2

traitlets=5.0.5

typing-extensions=3.10.0.0

typing_extensions=3.10.0.0

unixodbc=2.3.9

urllib3=1.26.4

wcwidth=0.2.5

webencodings=0.5.1

werkzeug=2.0.1

wheel=0.36.2

wrapt=1.12.1

xgboost=1.4.0

xorg-kbproto=1.0.7

xorg-libice=1.0.10

xorg-libsm=1.2.3

xorg-libx11=1.7.2

xorg-libxext=1.3.4

xorg-libxrender=0.9.10

xorg-renderproto=0.11.1

xorg-xextproto=7.3.0

xorg-xproto=7.0.31

xz=5.2.5

yaml=0.2.5

yarl=1.6.3

zeromq=4.3.4

zfp=0.5.5

zipp=3.5.0

zlib=1.2.11

zope.event=4.5.0

zope.interface=5.4.0

zstd=1.4.9

azure-common=1.1.27

azure-core=1.16.0

azure-graphrbac=0.61.1

azure-mgmt-authorization=0.61.0

azure-mgmt-containerregistry=8.0.0

azure-mgmt-core=1.3.0

azure-mgmt-keyvault=2.2.0

azure-mgmt-resource=13.0.0

azure-mgmt-storage=11.2.0

azureml-core=1.34.0

azureml-mlflow=1.34.0

azureml-opendatasets=1.34.0

backports-tempfile=1.0

backports-weakref=1.0.post1

contextlib2=0.6.0.post1

docker=4.4.4

ipywidgets=7.6.3

jeepney=0.6.0

jmespath=0.10.0

jsonpickle=2.0.0

msrestazure=0.6.4

mypy=0.780

mypy-extensions=0.4.3

ndg-httpsclient=0.5.1

pandasql=0.7.3

pathspec=0.8.1

ruamel-yaml=0.17.4

ruamel-yaml-clib=0.2.6

secretstorage=3.3.1

sqlalchemy=1.4.20

typed-ast=1.4.3

torchvision=0.9.1

websocket-client=1.1.0


## Next steps

- [Azure Synapse Analytics](../overview-what-is.md)
- [Apache Spark Documentation](https://spark.apache.org/docs/3.0.2/)
- [Apache Spark Concepts](apache-spark-concepts.md)
