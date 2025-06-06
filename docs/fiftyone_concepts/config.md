# Configuring FiftyOne [¶](\#configuring-fiftyone "Permalink to this headline")

FiftyOne can be configured in various ways. This guide covers the various
options that exist, how to view your current config, and how to customize your
config as desired.

## Configuration options [¶](\#configuration-options "Permalink to this headline")

FiftyOne supports the configuration options described below:

| Config field | Environment variable | Default value | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| --- | --- | --- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `database_admin` | `FIFTYONE_DATABASE_ADMIN` | `True` | Whether the client is allowed to trigger database migrations. See<br>[this section](#database-migrations) for more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `database_dir` | `FIFTYONE_DATABASE_DIR` | `~/.fiftyone/var/lib/mongo` | The directory in which to store FiftyOne’s backing database. Only applicable if<br>`database_uri` is not defined.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `database_name` | `FIFTYONE_DATABASE_NAME` | `fiftyone` | A name to use for FiftyOne’s backing database in your MongoDB instance. The database<br>is automatically created if necessary.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `database_uri` | `FIFTYONE_DATABASE_URI` | `None` | A [MongoDB URI](https://docs.mongodb.com/manual/reference/connection-string/) to<br>specifying a custom MongoDB database to which to connect. See<br>[this section](#configuring-mongodb-connection) for more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `database_validation` | `FIFTYONE_DATABASE_VALIDATION` | `True` | Whether to validate the compatibility of database before connecting to it. See<br>[this section](#configuring-mongodb-connection) for more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `dataset_zoo_dir` | `FIFTYONE_DATASET_ZOO_DIR` | `~/fiftyone` | The default directory in which to store datasets that are downloaded from the<br>[FiftyOne Dataset Zoo](../data/dataset_zoo/index.md#dataset-zoo).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `dataset_zoo_manifest_paths` | `FIFTYONE_ZOO_MANIFEST_PATHS` | `None` | A list of manifest JSON files specifying additional zoo datasets. See<br>[adding datasets to the zoo](../data/dataset_zoo/api.md#dataset-zoo-add) for more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `default_dataset_dir` | `FIFTYONE_DEFAULT_DATASET_DIR` | `~/fiftyone` | The default directory to use when performing FiftyOne operations that<br>require writing dataset contents to disk, such as ingesting datasets via<br>[`ingest_labeled_images()`](../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.ingest_labeled_images "fiftyone.core.dataset.Dataset.ingest_labeled_images").                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `default_ml_backend` | `FIFTYONE_DEFAULT_ML_BACKEND` | `torch` | The default ML backend to use when performing operations such as<br>downloading datasets from the FiftyOne Dataset Zoo that support multiple ML<br>backends. Supported values are `torch` and `tensorflow`. By default,<br>`torch` is used if [PyTorch](https://pytorch.org) is installed in your<br>Python environment, and `tensorflow` is used if<br>[TensorFlow](http://tensorflow.org) is installed. If no supported backend<br>is detected, this defaults to `None`, and any operation that requires an<br>installed ML backend will raise an informative error message if invoked in<br>this state.                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `default_batch_size` | `FIFTYONE_DEFAULT_BATCH_SIZE` | `None` | A default batch size to use when [applying models to datasets](../models/model_zoo/api.md#model-zoo-apply).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `default_batcher` | `FIFTYONE_DEFAULT_BATCHER` | `latency` | Batching implementation to use in some batched database operations such as<br>[`add_samples()`](../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.add_samples "fiftyone.core.dataset.Dataset.add_samples"),<br>[`set_values()`](../api/fiftyone.core.collections.html#fiftyone.core.collections.SampleCollection.set_values "fiftyone.core.collections.SampleCollection.set_values"), and<br>[`save_context()`](../api/fiftyone.core.collections.html#fiftyone.core.collections.SampleCollection.save_context "fiftyone.core.collections.SampleCollection.save_context").<br>Supported values are `latency`, `size`, and `static`.<br>`latency` is the default, which uses a dynamic batch size to achieve a target latency<br>of `batcher_target_latency` between calls. The default changes to `size` for the<br>FiftyOne Teams SDK in [API connection mode](../teams/api_connection.md#teams-api-connection), which targets<br>a size of `batcher_target_size_bytes` for each call. `static` uses a fixed batch size<br>of `batcher_static_size`. |
| `batcher_static_size` | `FIFTYONE_BATCHER_STATIC_SIZE` | `100` | Fixed size of batches. Only used when `default_batcher` is `static`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `batcher_target_size_bytes` | `FIFTYONE_BATCHER_TARGET_SIZE_BYTES` | `2 ** 20` | Target content size of batches, in bytes. Only used when `default_batcher` is `size`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `batcher_target_latency` | `FIFTYONE_BATCHER_TARGET_LATENCY` | `0.2` | Target latency between batches, in seconds. Only used when `default_batcher` is<br>`latency`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `default_sequence_idx` | `FIFTYONE_DEFAULT_SEQUENCE_IDX` | `%06d` | The default numeric string pattern to use when writing sequential lists of<br>files.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `default_image_ext` | `FIFTYONE_DEFAULT_IMAGE_EXT` | `.jpg` | The default image format to use when writing images to disk.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `default_video_ext` | `FIFTYONE_DEFAULT_VIDEO_EXT` | `.mp4` | The default video format to use when writing videos to disk.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `default_app_port` | `FIFTYONE_DEFAULT_APP_PORT` | `5151` | The default port to use to serve the [FiftyOne App](app.md#fiftyone-app).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `default_app_address` | `FIFTYONE_DEFAULT_APP_ADDRESS` | `localhost` | The default address to use to serve the [FiftyOne App](app.md#fiftyone-app). This may<br>be either an IP address or hostname. If it’s a hostname, the App will listen to all<br>IP addresses associated with the name. The default is `localhost`, which means the App<br>will only listen on the local interface. See [this page](running_environments.md#restricting-app-address)<br>for more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `do_not_track` | `FIFTYONE_DO_NOT_TRACK` | `False` | Controls whether UUID based import and App usage events are tracked.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `logging_level` | `FIFTYONE_LOGGING_LEVEL` | `INFO` | Controls FiftyOne’s package-wide logging level. Can be any valid `logging` level as<br>a string: `DEBUG, INFO, WARNING, ERROR, CRITICAL`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `max_thread_pool_workers` | `FIFTYONE_MAX_THREAD_POOL_WORKERS` | `None` | An optional maximum number of workers to use when creating thread pools                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `max_process_pool_workers` | `FIFTYONE_MAX_PROCESS_POOL_WORKERS` | `None` | An optional maximum number of workers to use when creating process pools                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `model_zoo_dir` | `FIFTYONE_MODEL_ZOO_DIR` | `~/fiftyone/__models__` | The default directory in which to store models that are downloaded from the<br>[FiftyOne Model Zoo](../models/model_zoo/index.md#model-zoo).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `model_zoo_manifest_paths` | `FIFTYONE_MODEL_ZOO_MANIFEST_PATHS` | `None` | A list of manifest JSON files specifying additional zoo models. See<br>[adding models to the zoo](../models/model_zoo/api.md#model-zoo-add) for more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `module_path` | `FIFTYONE_MODULE_PATH` | `None` | A list of modules that should be automatically imported whenever FiftyOne is imported.<br>See [this page](using_datasets.md#custom-embedded-documents) for an example usage.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `operator_timeout` | `FIFTYONE_OPERATOR_TIMEOUT` | `600` | The timeout for execution of an operator. See [this page](../plugins/index.md#fiftyone-plugins) for<br>more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `allow_legacy_orchestrators` | `FIFTYONE_ALLOW_LEGACY_ORCHESTRATORS` | `False` | Whether to allow delegated operations to be scheduled locally.<br>See [this page](../plugins/using_plugins.md#delegated-orchestrator-open-source) for more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `plugins_dir` | `FIFTYONE_PLUGINS_DIR` | `None` | A directory containing custom App plugins. See [this page](../plugins/index.md#fiftyone-plugins) for<br>more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `plugins_cache_enabled` | `FIFTYONE_PLUGINS_CACHE_ENABLED` | `False` | When set to `True` plugins will be cached until their directory’s `mtime` changes.<br>This is intended to be used in production.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `do_not_track` | `FIFTYONE_DO_NOT_TRACK` | `False` | Controls whether UUID based import and App usage events are tracked.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `show_progress_bars` | `FIFTYONE_SHOW_PROGRESS_BARS` | `True` | Controls whether progress bars are printed to the terminal when performing<br>operations such reading/writing large datasets or activating FiftyOne<br>Brain methods on datasets.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `timezone` | `FIFTYONE_TIMEZONE` | `None` | An optional timezone string. If provided, all datetimes read from FiftyOne datasets<br>will be expressed in this timezone. See [this section](#configuring-timezone) for<br>more information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

## Viewing your config [¶](\#viewing-your-config "Permalink to this headline")

You can print your current FiftyOne config at any time via the Python library
and the CLI:

Note

If you have customized your FiftyOne config via any of the methods
described below, printing your config is a convenient way to ensure that
the changes you made have taken effect as you expected.

## Modifying your config [¶](\#modifying-your-config "Permalink to this headline")

You can modify your FiftyOne config in a variety of ways. The following
sections describe these options in detail.

### Order of precedence [¶](\#order-of-precedence "Permalink to this headline")

The following order of precedence is used to assign values to your FiftyOne
config settings at runtime:

1. Config changes applied at runtime by directly editing `fiftyone.config`

2. `FIFTYONE_XXX` environment variables

3. Settings in your JSON config ( `~/.fiftyone/config.json`)

4. The default config values

### Editing your JSON config [¶](\#editing-your-json-config "Permalink to this headline")

You can permanently customize your FiftyOne config by creating a
`~/.fiftyone/config.json` file on your machine. The JSON file may contain any
desired subset of config fields that you wish to customize.

For example, a valid config JSON file is:

```python
{
    "default_ml_backend": "tensorflow",
    "show_progress_bars": true
}

```

When `fiftyone` is imported, any options from your JSON config are applied,
as per the order of precedence described above.

Note

You can customize the location from which your JSON config is read by
setting the `FIFTYONE_CONFIG_PATH` environment variable.

### Setting environment variables [¶](\#setting-environment-variables "Permalink to this headline")

FiftyOne config settings may be customized on a per-session basis by setting
the `FIFTYONE_XXX` environment variable(s) for the desired config settings.

When `fiftyone` is imported, all config environment variables are applied, as
per the order of precedence described above.

For example, you can customize your FiftyOne config in a Terminal session by
issuing the following commands prior to launching your Python interpreter:

```python
export FIFTYONE_DEFAULT_ML_BACKEND=tensorflow
export FIFTYONE_SHOW_PROGRESS_BARS=true

```

### Modifying your config in code [¶](\#modifying-your-config-in-code "Permalink to this headline")

You can dynamically modify your FiftyOne config at runtime by editing the
`fiftyone.config` object.

Any changes to your FiftyOne config applied via this manner will immediately
take effect for all subsequent calls to `fiftyone.config` during your current
session.

```python
import fiftyone as fo

fo.config.default_ml_backend = "tensorflow"
fo.config.show_progress_bars = True

```

## Configuring a MongoDB connection [¶](\#configuring-a-mongodb-connection "Permalink to this headline")

By default, FiftyOne is installed with its own MongoDB database distribution.
This database is managed by FiftyOne automatically as a service that runs
whenever at least one FiftyOne Python client is alive.

Alternatively, you can configure FiftyOne to connect to your own self-managed
MongoDB instance. To do so, simply set the `database_uri` property of your
FiftyOne config to any valid
[MongoDB connection string URI](https://docs.mongodb.com/manual/reference/connection-string/).

You can achieve this by adding the following entry to your
`~/.fiftyone/config.json` file:

```python
{
    "database_uri": "mongodb://[username:password@]host[:port]"
}

```

or you can set the following environment variable:

```python
export FIFTYONE_DATABASE_URI=mongodb://[username:password@]host[:port]

```

If you are running MongoDB with authentication enabled (the `--auth` flag),
FiftyOne must connect as a root user.

You can create a root user with the Mongo shell as follows:

```python
mongo --shell
> use admin
> db.createUser({user: "username", pwd: passwordPrompt(), roles: ["root"]})

```

You must also add `?authSource=admin` to your database URI:

```python
mongodb://[username:password@]host[:port]/?authSource=admin

```

### Using a different MongoDB version [¶](\#using-a-different-mongodb-version "Permalink to this headline")

FiftyOne is designed for **MongoDB v4.4 or later**.

If you wish to connect FiftyOne to a MongoDB database whose version is not
explicitly supported, you will also need to set the `database_validation`
property of your FiftyOne config to `False` to suppress a runtime error that
will otherwise occur.

You can achieve this by adding the following entry to your
`~/.fiftyone/config.json` file:

```python
{
    "database_validation": false
}

```

or you can set the following environment variable:

```python
export FIFTYONE_DATABASE_VALIDATION=false

```

### Controlling database migrations [¶](\#controlling-database-migrations "Permalink to this headline")

If you are working with a shared MongoDB database, you can use
[database admin privileges](#database-migrations) to control which clients
are allowed to migrate the shared database.

### Example custom database usage [¶](\#example-custom-database-usage "Permalink to this headline")

In order to use a custom MongoDB database with FiftyOne, you must manually
start the database before importing FiftyOne. MongoDB provides
[a variety of options](https://docs.mongodb.com/manual/tutorial/manage-mongodb-processes)
for this, including running the database as a daemon automatically.

In the simplest case, you can just run `mongod` in one shell:

```python
mkdir -p /path/for/db
mongod --dbpath /path/for/db

```

Then, in another shell, configure the database URI and launch FiftyOne:

```python
export FIFTYONE_DATABASE_URI=mongodb://localhost

```

```python
import fiftyone as fo
import fiftyone.zoo as foz

dataset = foz.load_zoo_dataset("quickstart")
session = fo.launch_app(dataset)

```

## Database migrations [¶](\#database-migrations "Permalink to this headline")

New FiftyOne versions occasionally introduce data model changes that require
database migrations when you [upgrade](../getting_started/basic/install.md#upgrading-fiftyone) or
[downgrade](../getting_started/basic/install.md#downgrading-fiftyone).

By default, database upgrades happen automatically in two steps:

- **Database**: when you import FiftyOne for the first time using a newer
version of the Python package, the database’s version is automatically
updated to match your client version

- **Datasets** are lazily migrated to the current database version on a
per-dataset basis whenever you load the dataset for the first time using a
newer version of the FiftyOne package

Database downgrades must be manually performed. See
[this page](../getting_started/basic/install.md#downgrading-fiftyone) for instructions.

You can use the [fiftyone migrate](../cli/index.md#cli-fiftyone-migrate) command to view
the current versions of your client, database, and datasets:

```python
# View your client, database, and dataset versions
fiftyone migrate --info

```

```python
Client version: 0.16.6
Compatible versions: >=0.16.3,<0.17

Database version: 0.16.6

dataset                      version
---------------------------  ---------
bdd100k-validation           0.16.5
quickstart                   0.16.5
...

```

### Restricting migrations [¶](\#restricting-migrations "Permalink to this headline")

You can use the `database_admin` config setting to control whether a client is
allowed to upgrade/downgrade your FiftyOne database. The default is `True`,
which means that upgrades are automatically performed when you connect to your
database with newer Python client versions.

If you set `database_admin` to `False`, your client will **never** cause the
database to be migrated to a new version. Instead, you’ll see the following
behavior:

- If your client is compatible with the current database version, you will be
allowed to connect to the database and use FiftyOne

- If your client is not compatible with the current database version, you
will see an informative error message when you import the library

You can restrict migrations by adding the following entry to your
`~/.fiftyone/config.json` file:

```python
{
    "database_admin": false
}

```

or by setting the following environment variable:

```python
export FIFTYONE_DATABASE_ADMIN=false

```

Note

A common pattern when working with
[custom/shared MongoDB databases](#configuring-mongodb-connection) is
to adopt a convention that all non-administrators set their
`database_admin` config setting to `False` to ensure that they cannot
trigger automatic database upgrades by connecting to the database with
newer Python client versions.

### Coordinating a migration [¶](\#coordinating-a-migration "Permalink to this headline")

If you are working in an environment where multiple services are connecting to
your MongoDB database at any given time, use this strategy to upgrade your
deployment:

1. Ensure that all clients are running without database admin privileges,
e.g., by adding this to their `~/.fiftyone/config.json`:

```python
{
    "database_admin": false
}

```

2. Perform a test upgrade of one client and ensure that it is compatible with
your current database version:

```python
# In a test environment
pip install --upgrade fiftyone

# View client's compatibility info
fiftyone migrate --info

```

```python
import fiftyone as fo

# Convince yourself that the new client can load a dataset
dataset = fo.load_dataset(...)

```

3. Now upgrade the client version used by all services:

```python
# In all client environments
pip install --upgrade fiftyone

```

4. Once all services are running the new client version, upgrade the database
with admin privileges:

```python
export FIFTYONE_DATABASE_ADMIN=true

pip install --upgrade fiftyone
fiftyone migrate --all

```

Note

Newly created datasets will always bear the
[`version`](../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.version "fiftyone.core.dataset.Dataset.version") of the Python
client that created them, which may differ from your database’s version
if you are undergoing a migration.

If the new client’s version is not in the compatibility range for the old
clients that are still in use, the old clients will not be able to load
the new datasets.

Therefore, it is recommended to upgrade all clients as soon as possible!

## Configuring a timezone [¶](\#configuring-a-timezone "Permalink to this headline")

By default, FiftyOne loads all datetimes in FiftyOne datasets as naive
`datetime` objects expressed in UTC time.

However, you can configure FiftyOne to express datetimes in a specific timezone
by setting the `timezone` property of your FiftyOne config.

The `timezone` property can be set to any timezone string supported by
`pytz.timezone()`, or `"local"` to use your current local timezone.

For example, you could set the `FIFTYONE_TIMEZONE` environment variable:

```python
# Local timezone
export FIFTYONE_TIMEZONE=local

# US Eastern timezone
export FIFTYONE_TIMEZONE=US/Eastern

```

Or, you can even dynamically change the timezone while you work in Python:

```python
from datetime import datetime
import fiftyone as fo

sample = fo.Sample(filepath="image.png", created_at=datetime.utcnow())

dataset = fo.Dataset()
dataset.add_sample(sample)

print(sample.created_at)
# 2021-08-24 20:24:09.723021

fo.config.timezone = "local"
dataset.reload()

print(sample.created_at)
# 2021-08-24 16:24:09.723000-04:00

```

Note

The `timezone` setting does not affect the internal database representation
of datetimes, which are always stored as UTC timestamps.

## Configuring the App [¶](\#configuring-the-app "Permalink to this headline")

The [FiftyOne App](app.md#fiftyone-app) can also be configured in various ways.
A new copy of your App config is applied to each [`Session`](../api/fiftyone.core.session.html#fiftyone.core.session.Session "fiftyone.core.session.Session") object that is
created when you launch the App. A session’s config can be inspected and
modified via the [`session.config`](../api/fiftyone.core.session.html#fiftyone.core.session.Session.config "fiftyone.core.session.Session.config")
property.

```python
import fiftyone as fo
import fiftyone.zoo as foz

dataset = foz.load_zoo_dataset("quickstart")
print(fo.app_config)

session = fo.launch_app(dataset)
print(session.config)

```

Note

For changes to a live session’s config to take effect in the App, you must
call [`session.refresh()`](../api/fiftyone.core.session.html#fiftyone.core.session.Session.refresh "fiftyone.core.session.Session.refresh") or
invoke another state-updating action such as `session.view = my_view`.

The FiftyOne App can be configured in the ways described below:

| Config field | Environment variable | Default value | Description |
| --- | --- | --- | --- |
| `color_by` | `FIFTYONE_APP_COLOR_BY` | `"field"` | Whether to color labels by their field name ( `"field"`), `label` value ( `"label"`), or<br>render each instance ID/trajectory index ( `"instance"`). |
| `color_pool` | `FIFTYONE_APP_COLOR_POOL` | See below | A list of browser supported color strings from which the App should draw from when<br>drawing labels (e.g., object bounding boxes). |
| `colorscale` | `FIFTYONE_APP_COLORSCALE` | `"viridis"` | The colorscale to use when rendering heatmaps in the App. See<br>[this section](using_datasets.md#heatmaps) for more details. |
| `default_query_performance` | `FIFTYONE_APP_DEFAULT_QUERY_PERFORMANCE` | `True` | Default if a user hasn’t selected a query performance mode in their current session. See<br>[this section](app.md#app-optimizing-query-performance) for more details. |
| `disable_frame_filtering` | `FIFTYONE_APP_DISABLE_FRAME_FILTERING` | `False` | Whether to disable frame filtering for video datasets in the App’s grid view. See<br>[this section](app.md#app-optimizing-query-performance) for more details. |
| `enable_query_performance` | `FIFTYONE_APP_ENABLE_QUERY_PERFORMANCE` | `True` | Whether to show the query performance toggle in the UI for users to select. See<br>[this section](app.md#app-optimizing-query-performance) for more details. |
| `grid_zoom` | `FIFTYONE_APP_GRID_ZOOM` | `5` | The zoom level of the App’s sample grid. Larger values result in larger samples (and thus<br>fewer samples in the grid). Supported values are `{0, 1, ..., 10}`. |
| `loop_videos` | `FIFTYONE_APP_LOOP_VIDEOS` | `False` | Whether to loop videos by default in the expanded sample view. |
| `media_fallback` | `FIFTYONE_APP_MEDIA_FALLBACK` | `False` | Whether to fall back to the default media field ( `"filepath"`) when the configured media<br>field’s value for a sample is not defined. |
| `multicolor_keypoints` | `FIFTYONE_APP_MULTICOLOR_KEYPOINTS` | `False` | Whether to independently coloy keypoint points by their index |
| `notebook_height` | `FIFTYONE_APP_NOTEBOOK_HEIGHT` | `800` | The height of App instances displayed in notebook cells. |
| `proxy_url` | `FIFTYONE_APP_PROXY_URL` | `None` | A URL string to override the default server URL. Useful for configuring the session<br>through a reverse proxy in notebook environments. |
| `show_confidence` | `FIFTYONE_APP_SHOW_CONFIDENCE` | `True` | Whether to show confidences when rendering labels in the App’s expanded sample view. |
| `show_index` | `FIFTYONE_APP_SHOW_INDEX` | `True` | Whether to show indexes when rendering labels in the App’s expanded sample view. |
| `show_label` | `FIFTYONE_APP_SHOW_LABEL` | `True` | Whether to show the label value when rendering detection labels in the App’s expanded<br>sample view. |
| `show_skeletons` | `FIFTYONE_APP_SHOW_SKELETONS` | `True` | Whether to show keypoint skeletons, if available. |
| `show_tooltip` | `FIFTYONE_APP_SHOW_TOOLTIP` | `True` | Whether to show the tooltip when hovering over labels in the App’s expanded sample view. |
| `theme` | `FIFTYONE_APP_THEME` | `"browser"` | The default theme to use in the App. Supported values are `{"browser", "dark", "light"}`.<br>If `"browser"`, your current theme will be persisted in your browser’s storage. |
| `use_frame_number` | `FIFTYONE_APP_USE_FRAME_NUMBER` | `False` | Whether to use the frame number instead of a timestamp in the expanded sample view. Only<br>applicable to video samples. |
| `plugins` | N/A | `{}` | A dict of plugin configurations. See [this section](#configuring-plugins) for<br>details. |

## Viewing your App config [¶](\#viewing-your-app-config "Permalink to this headline")

You can print your App config at any time via the Python library and the CLI:

Note

If you have customized your App config via any of the methods described
below, printing your config is a convenient way to ensure that the changes
you made have taken effect as you expected.

## Modifying your App config [¶](\#modifying-your-app-config "Permalink to this headline")

You can modify your App config in a variety of ways. The following sections
describe these options in detail.

Note

Did you know? You can also configure the behavior of the App on a
per-dataset basis by customizing your
[dataset’s App config](using_datasets.md#dataset-app-config).

### Order of precedence [¶](\#id4 "Permalink to this headline")

The following order of precedence is used to assign values to your App config
settings at runtime:

1. Config settings of a
[`Session`](../api/fiftyone.core.session.html#fiftyone.core.session.Session "fiftyone.core.session.Session") instance in question

2. App config settings applied at runtime by directly editing
`fiftyone.app_config`

3. `FIFTYONE_APP_XXX` environment variables

4. Settings in your JSON App config ( `~/.fiftyone/app_config.json`)

5. The default App config values

### Launching the App with a custom config [¶](\#launching-the-app-with-a-custom-config "Permalink to this headline")

You can launch the FiftyOne App with a customized App config on a one-off basis
via the following pattern:

```python
import fiftyone as fo
import fiftyone.zoo as foz

dataset = foz.load_zoo_dataset("quickstart")

# Create a custom App config
app_config = fo.app_config.copy()
app_config.show_confidence = False
app_config.show_label = False

session = fo.launch_app(dataset, config=app_config)

```

You can also configure a live [`Session`](../api/fiftyone.core.session.html#fiftyone.core.session.Session "fiftyone.core.session.Session") by editing its
[`session.config`](../api/fiftyone.core.session.html#fiftyone.core.session.Session.config "fiftyone.core.session.Session.config") property and
calling [`session.refresh()`](../api/fiftyone.core.session.html#fiftyone.core.session.Session.refresh "fiftyone.core.session.Session.refresh") to
apply the changes:

```python
# Customize the config of a live session
session.config.show_confidence = True
session.config.show_label = True
session.refresh()  # must refresh after edits

```

### Editing your JSON App config [¶](\#editing-your-json-app-config "Permalink to this headline")

You can permanently customize your App config by creating a
`~/.fiftyone/app_config.json` file on your machine. The JSON file may contain
any desired subset of config fields that you wish to customize.

For example, a valid App config JSON file is:

```python
{
    "show_confidence": false,
    "show_label": false
}

```

When `fiftyone` is imported, any options from your JSON App config are applied,
as per the order of precedence described above.

Note

You can customize the location from which your JSON App config is read by
setting the `FIFTYONE_APP_CONFIG_PATH` environment variable.

### Setting App environment variables [¶](\#setting-app-environment-variables "Permalink to this headline")

App config settings may be customized on a per-session basis by setting the
`FIFTYONE_APP_XXX` environment variable(s) for the desired App config settings.

When `fiftyone` is imported, all App config environment variables are applied,
as per the order of precedence described above.

For example, you can customize your App config in a Terminal session by
issuing the following commands prior to launching your Python interpreter:

```python
export FIFTYONE_APP_SHOW_CONFIDENCE=false
export FIFTYONE_APP_SHOW_LABEL=false

```

### Modifying your App config in code [¶](\#modifying-your-app-config-in-code "Permalink to this headline")

You can dynamically modify your App config at runtime by editing the
`fiftyone.app_config` object.

Any changes to your App config applied via this manner will immediately
take effect for all subsequent calls to `fiftyone.app_config` during your
current session.

```python
import fiftyone as fo

fo.app_config.show_confidence = False
fo.app_config.show_label = False

```

## Configuring plugins [¶](\#configuring-plugins "Permalink to this headline")

You can store system-wide plugin configurations under the `plugins` key of your
App config.

Builtin plugins that you can configure include:

- The builtin [Map panel](app.md#app-map-panel)

- The builtin [3D visualizer](app.md#app-3d-visualizer-config)

- Any [custom plugins](../plugins/index.md#fiftyone-plugins) that you’ve registered

For example, you may add the following to your JSON App config
( `~/.fiftyone/app_config.json`) to register a Mapbox token globally on your
system:

```python
{
    "plugins": {
        "map": {
            "mapboxAccessToken": "XXXXXXXX"
        }
    }
}

```

Note

You can also store dataset-specific plugin settings by storing any subset
of the above values on a [dataset’s App config](using_datasets.md#dataset-app-config).

## Configuring a proxy URL [¶](\#configuring-a-proxy-url "Permalink to this headline")

When running FiftyOne in a cloud machine, such as a
[SageMaker Notebook](https://aws.amazon.com/sagemaker/notebooks/), a
`proxy_url` should be set in your
[FiftyOne App config](#configuring-fiftyone-app) before launching the App
in order for browser windows or notebook cells to point to a correct App URL.
For [SageMaker Notebooks](https://aws.amazon.com/sagemaker/notebooks/), the
below code snippet shows how to configure the proxy based on your instance.

```python
import fiftyone as fo

# before launching the App, configure a proxy_url
fo.app_config.proxy_url = "https://<myinstance>.notebook.<region>.sagemaker.aws/proxy/<port>/"

session = fo.launch_app(port=<port>)

```
