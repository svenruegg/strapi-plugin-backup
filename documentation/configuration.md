# Configuration

- [General](#general)
- [Storage](#storage)
- [Database](#database)
- [Uploads](#uploads)
- [Cleanup](#cleanup)

## General

### cronSchedule

- Required
- String

Run the backup on a custom cron schedule.

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      cronSchedule: '0 * * * *', // Run backup each hour
    }
  }
});
```

### errorHandler

- Optional
- Function
- Default : `errorHandler: (error, strapi) => {}`

Handling errors when a backup or cleanup operation fails.

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      errorHandler: (error, strapi) => {
        console.log(error);
      }
    }
  }
});
```

## Storage

### storageService

- Required
- String

The cloud storage service where backups are persisted.

Available options :

* `aws-s3` for [AWS Simple Storage Service](https://aws.amazon.com/s3)

You must have `s3:PutObject`, `s3:ListBucket`, `s3:DeleteObject` in your IAM permissions.

`aws-s3` may work with S3 compatible services by using the `awsS3Endpoint` option instead of `awsRegion`.

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      storageService: 'aws-s3',
      awsAccessKeyId: '<AWS_ACCESS_KEY_ID>',
      awsSecretAccessKey: '<AWS_SECRET_ACCESS_KEY>',
      awsRegion: '<AWS_REGION>', // Optional if awsS3Endpoint is defined
      awsS3Endpoint: '<S3_ENDPOINT>', // Optional if awsRegion is defined (e.g. "s3.fr-par.scw.cloud")
      awsS3Bucket: '<AWS_S3_BUCKET>'
    }
  }
});
```

* `azure-blob-storage` for [Azure Blob Storage](https://azure.microsoft.com/products/storage/blobs)

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      storageService: 'azure-blob-storage',
      azureStorageAccountName: '<AZURE_STORAGE_ACCOUNT_NAME>',
      azureStorageAccountKey: '<AZURE_STORAGE_ACCOUNT_KEY>',
      azureStorageContainerName: '<AZURE_STORAGE_CONTAINER_NAME>',
    }
  }
});
```

* `gcs` for [Google Cloud Storage](https://cloud.google.com/storage)

You must have `storage.objects.create`, `storage.objects.list`, `storage.objects.delete` in your IAM permissions.

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      storageService: 'gcs',
      gcsKeyFilename: '/path/to/your/key/filename', // Full path to the a .json, .pem, or .p12 key downloaded from the Google Developers Console.
      gcsBucketName: '<GCS_BUCKET_NAME>'
    }
  }
});
```

## Database

### databaseDriver

- Required
- String (`sqlite` or `postgres` or `mysql` `strapi-export`)
- Default : environment variable `DATABASE_CLIENT`

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      databaseDriver: env('DATABASE_CLIENT'),
    }
  }
});
```

### mysqldumpExecutable

- Required if your strapi database client is `mysql`
- String

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      mysqldumpExecutable: '/path/to/your/mysqldump/bin',
    }
  }
});
```

[Learn more about mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)

### mysqldumpOptions

- Optional
- Array

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      mysqldumpOptions: [
        '--add-drop-table',
        '--extended-insert',
        '--lock-tables',
        '--dump-date'
      ],
    }
  }
});
```

[Learn more about mysqldump options](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)

### pgDumpExecutable

- Required if your strapi database client is `postgres`
- String

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      pgDumpExecutable: '/path/to/your/pg_dump/bin',
    }
  }
});
```

[Learn more about pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html)

### pgDumpOptions

- Optional
- Array

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      pgDumpOptions: [
        '--clean',
        '--if-exists'
      ],
    }
  }
});
```

[Learn more about pg_dump options](https://www.postgresql.org/docs/current/app-pgdump.html)

### sqlite3Executable

- Required your strapi database client is `sqlite`
- Array

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      sqlite3Executable: '/path/to/your/sqlite3/bin'
    }
  }
});
```

[Learn more about sqlite3](https://www.sqlite.org/cli.html)

### strapiExportOptions

- Required if you want to use `strapi-export`
- Type: Object
- Default: `strapiExportOptions: {'no-encrypt': true, exclude: 'files}`

Options to be passed to the strapi export command. This allows you to customize the export process based on the available options in Strapi.
Add a key if you want to encrypt the export.

Learn more about Strapi Export an the export options in the [official docs](https://docs.strapi.io/dev-docs/data-management/export)

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      strapiExportOptions: {
        only: 'content-types', // Optional String: Export only specific type. Available Options: `content`, `files`, `config`
        exclude: 'files', // Optional String: Allows to exlude `content`, `files`, `configuration`. Multiple options possible with comma separation.
        'no-encrypt': false, // Optional Boolean: Disable default encryption.
        key: 'my-encryption-key' // Optional String: To use encryption an encryption key is needed.
      }
    }
  }
});
```


### customDatabaseBackupFilename

- Optional
- Function

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      customDatabaseBackupFilename: () => `strapi-database-backup-${Date.now()}`,
    }
  }
});
```

### disableDatabaseBackup

- Optional
- Boolean
- Default : `false`

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      disableDatabaseBackup: true
    }
  }
});
```

## Uploads

### customUploadsBackupFilename

- Optional
- Function

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      customUploadsBackupFilename: () => `strapi-uploads-backup-${Date.now()}`,
    }
  }
});
```

### disableUploadsBackup

- Optional
- Boolean
- Default : `false`

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      disableUploadsBackup: true
    }
  }
});
```

## Cleanup

### allowCleanup

- Optional
- Boolean
- Default : `false`

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      allowCleanup: true
    }
  }
});
```

### timeToKeepBackupsInSeconds

- Required if `allowCleanup` is `true`
- Number

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      timeToKeepBackupsInSeconds: 172800, // Keeps backups for 2 days
    }
  }
});
```

### cleanupCronSchedule

- Optional
- String
- Default : `config.cronSchedule`

Run the cleanup on a custom cron schedule.

```js
// ./config/plugins.js

module.exports = ({env}) => ({
  backup: {
    enabled: true,
    config: {
      cleanupCronSchedule: '0 0 * * *', // Run cleanup each day at 00:00
    }
  }
});
```
