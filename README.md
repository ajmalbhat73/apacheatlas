Apache Atlas Docker image

Apache Atlas is built from the 2.1.0-release source tarball.

Atlas is built with `embedded HBase + Solr` and it is pre-initialized.

Basic usage
-----------
1. Pull the latest release image:

```bash
docker pull ajmalbhat73/apache-atlas
```

2. Start Apache Atlas in a container exposing Web-UI port 21000:

```bash
docker run -d \
    -p 21000:21000 \
    --name atlas \
    ajmalbhat73/apache-atlas 
```

Web-UI default credentials: `admin / admin`

Usage options
-------------

Gracefully stop Atlas:

```bash
docker exec -ti atlas /opt/apache-atlas-2.1.0/bin/atlas_stop.py
```

Check Atlas startup script output:

```bash
docker logs atlas
```

Check interactively Atlas application.log (useful at the first run and for debugging during workload):

```bash
docker exec -ti atlas tail -f /opt/apache-atlas-2.1.0/logs/application.log
```

Run the example (this will add sample types and instances along with traits):

```bash
docker exec -ti atlas /opt/apache-atlas-2.1.0/bin/quick_start.py
```

Start Atlas overriding settings by environment variables 
(to support large number of metadata objects for example):

```bash
docker run --detach \
    -e "ATLAS_SERVER_OPTS=-server -XX:SoftRefLRUPolicyMSPerMB=0 \
    -XX:+CMSClassUnloadingEnabled -XX:+UseConcMarkSweepGC \
    -XX:+CMSParallelRemarkEnabled -XX:+PrintTenuringDistribution \
    -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=dumps/atlas_server.hprof \
    -Xloggc:logs/gc-worker.log -verbose:gc -XX:+UseGCLogFileRotation \
    -XX:NumberOfGCLogFiles=10 -XX:GCLogFileSize=1m -XX:+PrintGCDetails \
    -XX:+PrintHeapAtGC -XX:+PrintGCTimeStamps" \
    -p 21000:21000 \
    --name atlas \
    ajmalbhat73/apache-atlas 
```

Start Atlas exposing logs directory on the host to view them directly:

```bash
docker run --detach \
    -v ${PWD}/atlas-logs:/opt/apache-atlas-2.1.0/logs \
    -p 21000:21000 \
    --name atlas \
    ajmalbhat73/apache-atlas 
```

Start Atlas exposing conf directory on the host to place and edit configuration files directly:

```bash
docker run --detach \
    -v ${PWD}/pre-conf:/opt/apache-atlas-2.1.0/conf \
    -p 21000:21000 \
    --name atlas \
    ajmalbhat73/apache-atlas 
```
Start Atlas with data directory mounted on the host to provide its persistency:
```bash
docker run --detach \
    -v ${PWD}/data:/opt/apache-atlas-2.1.0/data \
    -p 21000:21000 \
    --name atlas \
    ajmalbhat73/apache-atlas
```

Environment Variables
---------------------

The following environment variables are available for configuration:

| Name | Default | Description |
|------|---------|-------------|
| JAVA_HOME | /usr/lib/jvm/java-8-openjdk-amd64 | The java implementation to use. If JAVA_HOME is not found we expect java and jar to be in path
| ATLAS_OPTS | <none> | any additional java opts you want to set. This will apply to both client and server operations
| ATLAS_CLIENT_OPTS | <none> | any additional java opts that you want to set for client only
| ATLAS_CLIENT_HEAP | <none> | java heap size we want to set for the client. Default is 1024MB
| ATLAS_SERVER_OPTS | <none> |  any additional opts you want to set for atlas service.
| ATLAS_SERVER_HEAP | <none> | java heap size we want to set for the atlas server. Default is 1024MB
| ATLAS_HOME_DIR | <none> | What is is considered as atlas home dir. Default is the base location of the installed software
| ATLAS_LOG_DIR | <none> | Where log files are stored. Defatult is logs directory under the base install location
| ATLAS_PID_DIR | <none> | Where pid files are stored. Defatult is logs directory under the base install location
| ATLAS_EXPANDED_WEBAPP_DIR | <none> | Where do you want to expand the war file. By Default it is in /server/webapp dir under the base install dir.


