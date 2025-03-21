---
title: Connect to a CockroachDB Cluster
summary: How to connect to a CockroachDB cluster from your application
toc: true
---

This page documents the required connection configuration for [fully-supported third-party tools](third-party-database-tools.html).

For a list of all supported cluster connection parameters, see the [`cockroach` Connection Parameters](connection-parameters.html).

For a list of community-supported third-party tools, see [Third-Party Tools Supported by the Community](community-tooling.html). CockroachDB supports both native drivers and the PostgreSQL wire protocol. Most client drivers and ORM frameworks connect to CockroachDB like they connect to PostgreSQL.

<div class="filter-content" markdown="1" data-scope="core">

{{site.data.alerts.callout_info}}
The connection information shown on this page uses [client certificate and key authentication](authentication.html#client-authentication) to connect to a secure, {{ site.data.products.core }} cluster.

To connect to a {{ site.data.products.core }} cluster with client certificate and key authentication, you must first [generate server and client certificates](authentication.html#using-digital-certificates-with-cockroachdb).

For instructions on starting a secure cluster, see [Start a Local Cluster (Secure)](secure-a-cluster.html).
{{site.data.alerts.end}}

</div>

<div class="filters clearfix">
  <button class="filter-button page-level" data-scope="serverless">{{ site.data.products.serverless }}</button>
  <button class="filter-button page-level" data-scope="dedicated">{{ site.data.products.dedicated }}</button>
  <button class="filter-button page-level" data-scope="core">{{ site.data.products.core }}</button>
</div>

<div class="filters clearfix">
  <button class="filter-button page-level" data-scope="js-ts">JavaScript/TypeScript</button>
  <button class="filter-button page-level" data-scope="python">Python</button>
  <button class="filter-button page-level" data-scope="go">Go</button>
  <button class="filter-button page-level" data-scope="java">Java</button>
  <button class="filter-button page-level" data-scope="ruby">Ruby</button>
</div>

<div class="filter-content" markdown="1" data-scope="js-ts">

<div class="filters clearfix">
  <button class="filter-button page-level" data-scope="node-postgres">node-postgres</button>
  <button class="filter-button page-level" data-scope="sequelize">Sequelize</button>
  <button class="filter-button page-level" data-scope="typeorm">TypeORM</button>
</div>

<div class="filter-content" markdown="1" data-scope="node-postgres">

To connect to CockroachDB with [node-postgres](https://node-postgres.com), create a new [`Client`](https://node-postgres.com/api/client) object with a connection string.

For example:

{% include copy-clipboard.html %}
~~~ js
const { Client } = require('pg')

const connectionString = process.env.DATABASE_URL
const client = new Client({
  connectionString,
})

client.connect()
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

node-postgres accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://<username>:<password>@<host>:<port>/<database>?sslmode=verify-full&options=--cluster%3D<routing-id>
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://<username>:<password>@<host>:<port>/<database>?sslmode=verify-full&sslrootcert=<root-cert>
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://<username>@<host>:<port>/<database>?sslmode=verify-full&sslrootcert=<root-cert>&sslcert=<client-cert>&sslkey=<client-key>
~~~

</div>

For more information about connecting with node-postgres, see the [official node-postgres documentation](https://node-postgres.com/features/connecting).

### Connection parameters

<div class="filter-content" markdown="1" data-scope="serverless">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<password>`  | The password for the SQL user connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.
`<routing-id>`  | Your cluster's routing ID (e.g., `funky-skunk-123`). The routing ID identifies your tenant cluster on a [multi-tenant host](../cockroachcloud/architecture.html#architecture).

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<password>`  | The password for the SQL user connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.
`<root-cert>`  | The path to the root certificate that you [downloaded from the CockroachDB Cloud Console](../cockroachcloud/authentication.html#node-identity-verification).

</div>

<div class="filter-content" markdown="1" data-scope="core">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.
`<root-cert>`  | The path to the root certificate.<br>You can generate this certificate with [`cockroach cert create-ca`](cockroach-cert.html#subcommands), or you can use a [custom CA cert](create-security-certificates-custom-ca.html).
`<client-cert>`  | The path to the [client certificate](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this certificate with [`cockroach cert create-client`](cockroach-cert.html#subcommands).
`<client-key>`  | The path to the [client key](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this key with [`cockroach cert create-client`](cockroach-cert.html#subcommands).

</div>

</div>

<div class="filter-content" markdown="1" data-scope="sequelize">

To connect to CockroachDB with [Sequelize](https://sequelize.org), create a `Sequelize` object with the [CockroachDB Sequelize adapter](https://github.com/cockroachdb/sequelize-cockroachdb).

For example:

{% include copy-clipboard.html %}
~~~ js
const Sequelize = require("sequelize-cockroachdb");

const connectionString = process.env.DATABASE_URL
const sequelize = new Sequelize(connectionString)
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

Sequelize versions 6.10 and later accept the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://<username>:<password>@<host>:<port>/<database>?sslmode=verify-full&options=--cluster%3D<routing-id>
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://<username>:<password>@<host>:<port>/<database>?sslmode=verify-full&sslrootcert=<root-cert>
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://<username>@<host>:<port>/<database>?sslmode=verify-full&sslrootcert=<root-cert>&sslcert=<client-cert>&sslkey=<client-key>
~~~

</div>

For Sequelize versions 6.9 and earlier, use the following format:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://<username>:<password>@<host>:<port>/<routing-id>.<database>?ssl=true
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://<username>:<password>@<host>:<port>/<database>?ssl=true&sslrootcert=<root-cert>
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://<username>@<host>:<port>/<database>?ssl=true&sslrootcert=<root-cert>&sslcert=<client-cert>&sslkey=<client-key>
~~~

</div>

{{site.data.alerts.callout_info}}
To connect to CockroachDB with Sequelize, you must install the [CockroachDB Sequelize adapter](https://github.com/cockroachdb/sequelize-cockroachdb).
{{site.data.alerts.end}}

For more information about connecting with Sequelize, see the [official Sequelize documentation](https://sequelize.org/master/index.html).

### Connection parameters

<div class="filter-content" markdown="1" data-scope="serverless">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<password>`  | The password for the SQL user connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.
`<routing-id>`  | Your cluster's routing ID (e.g., `funky-skunk-123`). The routing ID identifies your tenant cluster on a [multi-tenant host](../cockroachcloud/architecture.html#architecture).

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<password>`  | The password for the SQL user connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.
`<root-cert>`  | The path to the root certificate that you [downloaded from the CockroachDB Cloud Console](../cockroachcloud/authentication.html#node-identity-verification).

</div>

<div class="filter-content" markdown="1" data-scope="core">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.
`<root-cert>`  | The path to the root certificate.<br>You can generate this certificate with [`cockroach cert create-ca`](cockroach-cert.html#subcommands), or you can use a [custom CA cert](create-security-certificates-custom-ca.html).
`<client-cert>`  | The path to the [client certificate](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this certificate with [`cockroach cert create-client`](cockroach-cert.html#subcommands).
`<client-key>`  | The path to the [client key](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this key with [`cockroach cert create-client`](cockroach-cert.html#subcommands).

</div>

</div>

<div class="filter-content" markdown="1" data-scope="typeorm">

<div>

To connect to CockroachDB with [TypeORM](https://typeorm.io), update your project's `ormconfig` file with the required connection properties.

For example, suppose that you have a file named `ormconfig.ts` in the project's root directory.

</div>

<div class="filter-content" markdown="1" data-scope="serverless">

CockroachDB {{ site.data.products.serverless }} requires you to specify the `type`, `url`, `ssl`, and `options: "--cluster"` properties:

{% include copy-clipboard.html %}
~~~ ts
module.exports = {
  type: "cockroachdb",
  url: process.env.DATABASE_URL,
  ssl: true,
  extra: {
      options: "--cluster=<routing-id>"
  },
  ...
};
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

TypeORM accepts the following format for CockroachDB connection strings:

{% include copy-clipboard.html %}
~~~
postgresql://<username>:<password>@<host>:<port>/<database>
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

CockroachDB {{ site.data.products.dedicated }} requires you to specify the `type`, `url`, and `ssl` properties:

{% include copy-clipboard.html %}
~~~ ts
module.exports = {
  type: "cockroachdb",
  url: process.env.DATABASE_URL,
  ssl: {
    ca: process.env.CA_CERT
  }
  ...
};
~~~

Where:

- `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.
- `CA_CERT` is an environment variable set to the root certificate [downloaded from the CockroachDB Cloud Console](../cockroachcloud/authentication.html#node-identity-verification).

TypeORM accepts the following format for CockroachDB connection strings:

{% include copy-clipboard.html %}
~~~
postgresql://<username>:<password>@<host>:<port>/<database>
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

CockroachDB {{ site.data.products.core }} requires you to specify the `type`, `url`, and `ssl` properties:

{% include copy-clipboard.html %}
~~~ ts
module.exports = {
  type: "cockroachdb",
  url: process.env.DATABASE_URL,
  ssl: {
    ca: process.env.CA_CERT,
    key: process.env.CLIENT_KEY,
    cert: process.env.CLIENT_CERT
  }
  ...
};
~~~

Where:

- `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.
- `CA_CERT` is an environment variable set to the root certificate.<br>You can generate this certificate with [`cockroach cert create-ca`](cockroach-cert.html#subcommands), or you can use a [custom CA cert](create-security-certificates-custom-ca.html).
- `CLIENT_KEY` is an environment variable set to the [client key](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this key with [`cockroach cert create-client`](cockroach-cert.html#subcommands).
- `CLIENT_CERT` is an environment variable set to the [client certificate](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this certificate with [`cockroach cert create-client`](cockroach-cert.html#subcommands).

TypeORM accepts the following format for CockroachDB connection strings:

{% include copy-clipboard.html %}
~~~
postgresql://<username>@<host>:<port>/<database>
~~~

</div>

You can then call `createConnection` without any parameters:

{% include copy-clipboard.html %}
~~~ ts
import {createConnection} from "typeorm";

// createConnection method will automatically read connection options
// from your ormconfig file or environment variables
const connection = await createConnection();
~~~

`createConnection` will use the properties in your `ormconfig` file.

For more information about connecting with TypeORM, see the [official TypeORM documentation](https://typeorm.io/#/connection).

### Connection parameters

<div class="filter-content" markdown="1" data-scope="serverless">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<password>`  | The password for the SQL user connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.
`<routing-id>`  | Your cluster's routing ID (e.g., `funky-skunk-123`). The routing ID identifies your tenant cluster on a [multi-tenant host](../cockroachcloud/architecture.html#architecture).

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<password>`  | The password for the SQL user connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.

</div>

<div class="filter-content" markdown="1" data-scope="core">

Parameter | Description
----------|------------
`<username>`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`<host>`  | The host on which the CockroachDB node is running.
`<port>`  | The port at which the CockroachDB node is listening.
`<database>`  | The name of the (existing) database.

</div>

</div>

</div>

<div class="filter-content" markdown="1" data-scope="python">

<div class="filters clearfix">
  <button class="filter-button page-level" data-scope="psycopg">Psycopg2</button>
  <button class="filter-button page-level" data-scope="sqlalchemy">SQLAlchemy</button>
  <button class="filter-button page-level" data-scope="django">Django</button>
</div>


<div class="filter-content" markdown="1" data-scope="serverless">

{{site.data.alerts.callout_info}}
To connect to a {{ site.data.products.serverless }} cluster from a Python application, you must have a valid CA certificate located at <code>~/.postgresql/root.crt</code>.<br>For instructions on downloading a CA certificate from the {{ site.data.products.db }} Console, see <a href="../cockroachcloud/connect-to-a-serverless-cluster.html">Connect to a {{ site.data.products.serverless }} Cluster</a>.
{{site.data.alerts.end}}

</div>

<div class="filter-content" markdown="1" data-scope="psycopg">

To connect to CockroachDB with [Psycopg2](https://www.psycopg.org), pass a connection string to the [`psycopg2.connect` function](https://www.psycopg.org/docs/connection.html).

For example:

{% include copy-clipboard.html %}
~~~ python
import psycopg2
import os

conn = psycopg2.connect(os.environ['DATABASE_URL'])
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

Psycopg2 accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://{username}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

For more information about connecting with Psycopg, see the [official Psycopg documentation](https://www.psycopg.org/docs).

</div>

<div class="filter-content" markdown="1" data-scope="sqlalchemy">

To connect to CockroachDB with [SQLAlchemy](http://docs.sqlalchemy.org/en/latest), [create an `Engine` object](https://docs.sqlalchemy.org/en/14/core/engines.html) by passing the connection string to the `create_engine` function.

For example:

{% include copy-clipboard.html %}
~~~ python
from sqlalchemy import create_engine
import os

engine = create_engine(os.environ['DATABASE_URL'])
engine.connect()
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

SQLAlchemy accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
cockroachdb://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
cockroachdb://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
cockroachdb://{username}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

{{site.data.alerts.callout_info}}
To connect to CockroachDB with SQLAlchemy, you must install the [CockroachDB SQLAlchemy adapter](https://github.com/cockroachdb/sqlalchemy-cockroachdb).
{{site.data.alerts.end}}

For more information about connecting with SQLAlchemy, see the [official SQLAlchemy documentation](https://docs.sqlalchemy.org/en/14/core/engines_connections.html).

</div>

<div class="filter-content" markdown="1" data-scope="django">

<div>

To connect to CockroachDB from a [Django](https://www.djangoproject.com) application, update the `DATABASES` property in the project's `settings.py` file.

Django accepts the following format for CockroachDB connection information:

</div>

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
## settings.py

...

DATABASES = {
    'default': {
        'ENGINE': 'django_cockroachdb',
        'NAME': '{database}',
        'USER': '{username}',
        'PASSWORD': '{password}',
        'HOST': '{host}',
        'PORT': '{port}',
        'OPTIONS': {
            'sslmode': 'verify-full',
            'options': '--cluster={routing-id}'
        },
    },
}

...
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
## settings.py

...

DATABASES = {
    'default': {
        'ENGINE': 'django_cockroachdb',
        'NAME': '{database}',
        'USER': '{username}',
        'PASSWORD': '{password}',
        'HOST': '{host}',
        'PORT': '{port}',
        'OPTIONS': {
            'sslmode': 'verify-full',
            'sslrootcert': os.path.expandvars('{root-cert}'),
        },
    },
}

...
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
## settings.py

...

DATABASES = {
    'default': {
        'ENGINE': 'django_cockroachdb',
        'NAME': '{database}',
        'USER': '{username}',
        'HOST': '{host}',
        'PORT': '{port}',
        'OPTIONS': {
            'sslmode': 'verify-full',
            'sslrootcert': os.path.expandvars('{root-cert}'),
            'sslcert': os.path.expandvars('{client-cert}'),
            'sslkey': os.path.expandvars('{client-key}'),
        },
    },
}

...
~~~

</div>

{{site.data.alerts.callout_info}}
To connect to CockroachDB with Django, you must install the [CockroachDB Django adapter](https://github.com/cockroachdb/django-cockroachdb).
{{site.data.alerts.end}}

For more information about connecting with Django, see the [official Django documentation](https://docs.djangoproject.com/en/3.0).

</div>

### Connection parameters

<div class="filter-content" markdown="1" data-scope="serverless">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{password}`  | The password for the SQL user connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{routing-id}`  | Your cluster's routing ID (e.g., `funky-skunk-123`). The routing ID identifies your tenant cluster on a [multi-tenant host](../cockroachcloud/architecture.html#architecture).

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{password}`  | The password for the SQL user connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{root-cert}`  | The path to the root certificate that you [downloaded from the CockroachDB Cloud Console](../cockroachcloud/authentication.html#node-identity-verification).

</div>

<div class="filter-content" markdown="1" data-scope="core">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{root-cert}`  | The path to the root certificate.<br>You can generate this certificate with [`cockroach cert create-ca`](cockroach-cert.html#subcommands), or you can use a [custom CA cert](create-security-certificates-custom-ca.html).
`{client-cert}`  | The path to the [client certificate](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this certificate with [`cockroach cert create-client`](cockroach-cert.html#subcommands).
`{client-key}`  | The path to the [client key](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this key with [`cockroach cert create-client`](cockroach-cert.html#subcommands).

</div>

</div>

<div class="filter-content" markdown="1" data-scope="go">

<div class="filters clearfix">
  <button class="filter-button page-level" data-scope="pgx">pgx</button>
  <button class="filter-button page-level" data-scope="pq">pq</button>
  <button class="filter-button page-level" data-scope="gorm">GORM</button>
</div>

<div class="filter-content" markdown="1" data-scope="pgx">

To connect to CockroachDB with [pgx](https://github.com/jackc/pgx), use the `pgx.Connect` function.

For example:

{% include copy-clipboard.html %}
~~~ go
package main

import (
  "context"
  "os"

  "github.com/jackc/pgx/v4"
)

func main() {
  conn, err := pgx.Connect(context.Background(), os.Getenv("DATABASE_URL"))
  if err != nil {
    log.Fatal(err)
  }
  defer conn.Close(context.Background())
}
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

pgx accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://{username}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

For more information about connecting with pgx, see the [official pgx documentation](https://pkg.go.dev/github.com/jackc/pgx).

</div>

<div class="filter-content" markdown="1" data-scope="pq">

To connect to CockroachDB with [pq](https://godoc.org/github.com/lib/pq), use the [`sql.Open` function](https://go.dev/doc/tutorial/database-access).

For example:

{% include copy-clipboard.html %}
~~~ go
package main

import (
	"database/sql"
  "os"

	_ "github.com/lib/pq"
)

func main() {
	db, err := sql.Open("postgres", os.Getenv("DATABASE_URL"))
	if err != nil {
		log.Fatal(err)
	}

}
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

pq accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://{username}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

For more information about connecting with pq, see the [official pq documentation](https://pkg.go.dev/github.com/lib/pq).

</div>

<div class="filter-content" markdown="1" data-scope="gorm">


To connect to CockroachDB with [GORM](http://gorm.io), use the `gorm.Open` function, with the GORM `postgres` driver.

For example:

{% include copy-clipboard.html %}
~~~ go
import (
  "os"

  "gorm.io/driver/postgres"
  "gorm.io/gorm"
)

db, err := gorm.Open(postgres.Open(os.Getenv("DATABASE_URL")), &gorm.Config{})
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

GORM accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://{username}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

For more information about connecting with GORM, see the [official GORM documentation](https://gorm.io/docs).

</div>

### Connection parameters

<div class="filter-content" markdown="1" data-scope="serverless">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{password}`  | The password for the SQL user connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{routing-id}`  | Your cluster's routing ID (e.g., `funky-skunk-123`). The routing ID identifies your tenant cluster on a [multi-tenant host](../cockroachcloud/architecture.html#architecture).

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{password}`  | The password for the SQL user connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{root-cert}`  | The path to the root certificate that you [downloaded from the CockroachDB Cloud Console](../cockroachcloud/authentication.html#node-identity-verification).

</div>

<div class="filter-content" markdown="1" data-scope="core">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{root-cert}`  | The path to the root certificate.<br>You can generate this certificate with [`cockroach cert create-ca`](cockroach-cert.html#subcommands), or you can use a [custom CA cert](create-security-certificates-custom-ca.html).
`{client-cert}`  | The path to the [client certificate](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this certificate with [`cockroach cert create-client`](cockroach-cert.html#subcommands).
`{client-key}`  | The path to the [client key](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this key with [`cockroach cert create-client`](cockroach-cert.html#subcommands).

</div>

</div>

<div class="filter-content" markdown="1" data-scope="java">

<div class="filters clearfix">
  <button class="filter-button page-level" data-scope="jdbc">JDBC</button>
  <button class="filter-button page-level" data-scope="hibernate">Hibernate</button>
</div>


<div class="filter-content" markdown="1" data-scope="jdbc">

To connect to CockroachDB with the [JDBC](https://jdbc.postgresql.org) driver, create a `DataSource` object ([`PGSimpleDataSource` or `PGPoolingDataSource`](https://jdbc.postgresql.org/documentation/head/ds-ds.html)), and set the connection string with the `setUrl` class method.

For example:

{% include copy-clipboard.html %}
~~~ java
PGSimpleDataSource ds = new PGSimpleDataSource();
ds.setUrl(System.getenv("DATABASE_URL"));
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

JDBC accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
jdbc:postgresql://{host}:{port}/{database}?user={username}&password={password}&sslmode=verify-full&sslfactory=org.postgresql.ssl.DefaultJavaSSLFactory&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
jdbc:postgresql://{host}:{port}/{database}?user={username}&password={password}&sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
jdbc:postgresql://{host}:{port}/{database}?user={username}&sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

For more information about connecting with JDBC, see the [official JDBC documentation](https://jdbc.postgresql.org/documentation/head/index.html).

</div>

<div class="filter-content" markdown="1" data-scope="hibernate">

To connect to CockroachDB with [Hibernate](https://hibernate.org/orm) ORM, set the object's `hibernate.connection.url` property to a valid CockroachDB connection string.

For example, if you are bootstrapping your application with a [`ServiceRegistry`](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#bootstrap-native-registry) object, first read from the Hibernate configuration file, and then set the `hibernate.connection.url` with the `applySetting` class method:

{% include copy-clipboard.html %}
~~~ java
StandardServiceRegistry standardRegistry = new StandardServiceRegistryBuilder()
    .configure( "hibernate.cfg.xml" ).applySetting("hibernate.connection.url", System.getenv("DATABASE_URL"))
    .build();

Metadata metadata = new MetadataSources( standardRegistry )
    .getMetadataBuilder()
    .build();

SessionFactory sessionFactory = metadata.getSessionFactoryBuilder()
    .build();
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

Hibernate accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
jdbc:postgresql://{host}:{port}/{database}?user={username}&password={password}&sslmode=verify-full&sslfactory=org.postgresql.ssl.DefaultJavaSSLFactory&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
jdbc:postgresql://{host}:{port}/{database}?user={username}&password={password}&sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
jdbc:postgresql://{host}:{port}/{database}?user={username}&sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

{{site.data.alerts.callout_info}}
To connect to CockroachDB with Hibernate, you must specify the [CockroachDB Hibernate dialect](install-client-drivers.html?filters=java#hibernate) in your `hibernate.cfg.xml` configuration file.
{{site.data.alerts.end}}

For more information about connecting with Hibernate, see the [official Hibernate documentation](https://hibernate.org/orm/documentation).

</div>

### Connection parameters

<div class="filter-content" markdown="1" data-scope="serverless">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{password}`  | The password for the SQL user connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{routing-id}`  | Your cluster's routing ID (e.g., `funky-skunk-123`). The routing ID identifies your tenant cluster on a [multi-tenant host](../cockroachcloud/architecture.html#architecture).

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{password}`  | The password for the SQL user connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{root-cert}`  | The [URL-encoded](https://en.wikipedia.org/wiki/Percent-encoding) path to the root certificate that you [downloaded from the CockroachDB Cloud Console](../cockroachcloud/authentication.html#node-identity-verification).

</div>

<div class="filter-content" markdown="1" data-scope="core">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{root-cert}`  | The [URL-encoded](https://en.wikipedia.org/wiki/Percent-encoding) path to the root certificate.<br>You can generate this certificate with [`cockroach cert create-ca`](cockroach-cert.html#subcommands), or you can use a [custom CA cert](create-security-certificates-custom-ca.html).
`{client-cert}`  | The [URL-encoded](https://en.wikipedia.org/wiki/Percent-encoding) path to the [client certificate](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this certificate with [`cockroach cert create-client`](cockroach-cert.html#subcommands).
`{client-key}`  | The [URL-encoded](https://en.wikipedia.org/wiki/Percent-encoding) path to the [PKCS#8](https://tools.ietf.org/html/rfc5208)-formatted [client key](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this key with [`cockroach cert create-client --also-generate-pkcs8-key`](cockroach-cert.html#subcommands).

</div>

</div>

<div class="filter-content" markdown="1" data-scope="ruby">

<div class="filters clearfix">
  <button class="filter-button page-level" data-scope="ruby-pg">pg</button>
  <button class="filter-button page-level" data-scope="activerecord">ActiveRecord</button>
</div>

{{site.data.alerts.callout_info}}
To connect to a {{ site.data.products.serverless }} cluster from a Ruby application, you must have a valid CA certificate located at <code>~/.postgresql/root.crt</code>.<br>For instructions on downloading a CA certificate from the {{ site.data.products.db }} Console, see <a href="../cockroachcloud/connect-to-a-serverless-cluster.html">Connect to a {{ site.data.products.serverless }} Cluster</a>.
{{site.data.alerts.end}}

<div class="filter-content" markdown="1" data-scope="ruby-pg">

To connect to CockroachDB with the [Ruby pg](https://rubygems.org/gems/pg) driver, use the `PG.connect` function.

For example:

{% include copy-clipboard.html %}
~~~ ruby
#!/usr/bin/env ruby

require 'pg'

conn = PG.connect(ENV['DATABASE_URL'])
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

pg accepts the following format for CockroachDB connection strings:

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://{username}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

For more information about connecting with pg, see the [official pg documentation](https://www.rubydoc.info/gems/pg).

</div>

<div class="filter-content" markdown="1" data-scope="activerecord">

<div>

To connect to CockroachDB with [ActiveRecord](https://github.com/rails/rails/tree/main/activerecord) from a Rails app, update the database configuration in `config/database.yml`:

~~~ yaml
default: &default
  adapter: cockroachdb
  url: <%= ENV['DATABASE_URL'] %>

...
~~~

Where `DATABASE_URL` is an environment variable set to a valid CockroachDB connection string.

ActiveRecord accepts the following format for CockroachDB connection strings:

</div>

<div class="filter-content" markdown="1" data-scope="serverless">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&options=--cluster%3D{routing-id}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

{% include copy-clipboard.html %}
~~~
postgresql://{username}:{password}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}
~~~

</div>

<div class="filter-content" markdown="1" data-scope="core">

{% include copy-clipboard.html %}
~~~
postgresql://{username}@{host}:{port}/{database}?sslmode=verify-full&sslrootcert={root-cert}&sslcert={client-cert}&sslkey={client-key}
~~~

</div>

{{site.data.alerts.callout_info}}
To connect to CockroachDB with ActiveRecord, you must install the [ActiveRecord CockroachDB adapter](https://rubygems.org/gems/activerecord-cockroachdb-adapter).
{{site.data.alerts.end}}

For more information about connecting with ActiveRecord, see the [official ActiveRecord documentation](https://guides.rubyonrails.org/active_record_querying.html).

</div>

### Connection parameters

<div class="filter-content" markdown="1" data-scope="serverless">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{password}`  | The password for the SQL user connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{routing-id}`  | Your cluster's routing ID (e.g., `funky-skunk-123`). The routing ID identifies your tenant cluster on a [multi-tenant host](../cockroachcloud/architecture.html#architecture).

</div>

<div class="filter-content" markdown="1" data-scope="dedicated">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{password}`  | The password for the SQL user connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{root-cert}`  | The path to the root certificate that you [downloaded from the CockroachDB Cloud Console](../cockroachcloud/authentication.html#node-identity-verification).

</div>

<div class="filter-content" markdown="1" data-scope="core">

Parameter | Description
----------|------------
`{username}`  | The [SQL user](authorization.html#sql-users) connecting to the cluster.
`{host}`  | The host on which the CockroachDB node is running.
`{port}`  | The port at which the CockroachDB node is listening.
`{database}`  | The name of the (existing) database.
`{root-cert}`  | The path to the root certificate.<br>You can generate this certificate with [`cockroach cert create-ca`](cockroach-cert.html#subcommands), or you can use a [custom CA cert](create-security-certificates-custom-ca.html).
`{client-cert}`  | The path to the [client certificate](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this certificate with [`cockroach cert create-client`](cockroach-cert.html#subcommands).
`{client-key}`  | The path to the [client key](cockroach-cert.html#client-key-and-certificates) for the user connecting to the cluster.<br>You can generate this key with [`cockroach cert create-client`](cockroach-cert.html#subcommands).

</div>

</div>

## See also

- [Install a Driver or ORM Framework](install-client-drivers.html)
- [Connection Pooling](connection-pooling.html)
- [`cockroach` Connection Parameters](connection-parameters.html)
- [Example Apps](example-apps.html)
