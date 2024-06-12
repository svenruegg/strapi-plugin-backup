# Strapi Plugin Backup

Automate the backup of uploads and database to the cloud.

## Features

- Database backup
- Uploads files backup
- Cleanup of backups

## Installation

```sh
npm install strapi-plugin-backup
```

## Configuration

Please
see [documentation](https://github.com/adebayohountondji/strapi-plugin-backup/blob/1.x/documentation/configuration.md)
for more information about configuration.

## Example

```js
// ./config/plugins.js

module.exports = ({env}) => {
  return ({
    // ...
    backup: {
      enabled: true,
      config: {
        cronSchedule: '0 9-17 * * *', // At minute 0 past every hour from 9 through 17
        storageService: 'aws-s3',
        awsAccessKeyId: '<AWS_ACCESS_KEY_ID>',
        awsSecretAccessKey: '<AWS_SECRET_ACCESS_KEY>',
        awsRegion: '<AWS_REGION>',
        awsS3Bucket: '<AWS_S3_BUCKET>',
        databaseDriver: env('DATABASE_CLIENT'),
        mysqldumpExecutable: '/path/to/your/mysqldump/bin',
        mysqldumpOptions: [
          '--add-drop-table',
          '--extended-insert',
          '--lock-tables',
          '--dump-date'
        ],
        allowCleanup: true,
        cleanupPolicies: {
        days: 30 // required, keep backups for this amount of days
        weeks: 12, // optional, keep a backup per week for this amount of weeks
        months: 12 // optional, keep a backup per month for this amount of months
      },
        cleanupCronSchedule: '0 9 * * *', // Each day at 09:00 AM
        errorHandler: (error, strapi) => {
          console.log(error);
        },
      }
    },
    // ...
  })
};
```

## Releases

This project follows the Semantic Versioning convention ([https://semver.org](https://semver.org)) for version
numbering.

## Security

If you discover any security-related issues, please email mail@adebayo.fr instead of using the issue tracker.

## License

Please see [License File](https://github.com/adebayohountondji/strapi-plugin-backup/blob/1.x/LICENSE) for more
information.
