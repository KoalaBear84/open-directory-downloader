# A NodeJS Wrapper around OpenDirectoryDownloader

[![npm](https://img.shields.io/npm/v/open-directory-downloader?style=for-the-badge)](https://npmjs.com/package/open-directory-downloader)

This is a wrapper around [KoalaBear84/OpenDirectoryDownloader](https://github.com/KoalaBear84/OpenDirectoryDownloader), exposing functionality and output of the CLI in JavaScript.

Open Directory Downloader is a CLI tool for indexing so-called *Open Directories* (ODs), which are HTTP servers exposing files and folders for browsing and downloading. For more info about Open Directories visit [the subreddit](https://reddit.com/r/opendirectories)

## Installation

```shell
npm i open-directory-downloader
```

Upon installation, the latest supported OpenDirectoryDownloader binary will automatically be downloaded from GitHub.  
If this should fail, a local installation can be used instead (see below).

For changelogs, please look at the [GitHub release page](https://github.com/Chaphasilor/open-directory-downloader/releases).

## Usage

```js
const odd = require(`open-directory-downloader`);

const indexer = new odd.OpenDirectoryDownloader();

indexer.scanUrl(url)
.then(scanResult => {
  console.log(scanResult)
  // {
  //   scannedUrl: `https://example.com/files`,
  //   scan: {...},
  //   jsonFile: `/full/path/to/scan/file`,
  //   urlFile: `/full/path/to/url/file`,
  //   reddit: `
  //     |**Url:** https://example.com/files/||[Urls file](https://gofile.io/?c=XXXXX)|
  //     |:-|-:|-:|
  //     |**Extension (Top 5)**|**Files**|**Size**|
  //     |.mp3|14,779|294.68 GiB|
  //     |.flac|1,449|52.59 GiB|
  //     |.mkv|7|33.3 GiB|
  //     |.zip|7|5.61 GiB|
  //     |.mp4|8|4.85 GiB|
  //     |**Dirs:** 3,623 **Ext:** 119|**Total:** 30,357|**Total:** 415.97 GiB|
  //     |**Date (UTC):** 2021-01-16 15:28:53|**Time:** 00:01:13|**Speed:** 1.4 MB/s (12 mbit)|
  //   `,
  //   credits: `
  //     ^(Created by [KoalaBear84's OpenDirectory Indexer](https://github.com/KoalaBear84/OpenDirectoryDownloader/))
  //   `,
  //   missingFileSizes: false,
  // }
}
```

## Compatible Versions of OpenDirectoryDownloader

| Wrapper Version | Supported ODD Versions (up to) | Included Version |
| --- | --- | --- |
| 4.0.4 | 1.9.7.2 | 1.9.7.2 |
| 4.0.3 | 1.9.7.2 | 1.9.6.1 |
| 4.0.2 | 1.9.7.2 | 1.9.5.5 |
| 4.0.1 | 1.9.7.2 | 1.9.4.6 |
| 4.0.0 | 1.9.7.2 | 1.9.4.6 |
| 3.1.2 | 1.9.7.2 | 1.9.4.6 |
| 3.1.1 | 1.9.7.2 | 1.9.4.5 |
| 3.1.0 | 1.9.7.2 | 1.9.4.4 |
| 3.0.0 | 1.9.4.3 | 1.9.3.9 |
| 2.0.0 | 1.9.4.3 | 1.9.3.8 |
| 1.1.0 | 1.9.4.3 | 1.9.3.6 |
| 1.0.0 | 1.9.3.1-1.9.3.5 | 1.9.3.3 |

Some intermediary releases might not be fully supported. It is recommended to use the versions that are included by default.

## API

### Class OpenDirectoryDownloader

#### OpenDirectoryDownloader(options)

*Constructor*

- `options` (`Object`) (optional): Additional options for the scan
  - `outputFile` (`String`) (optional, defaults to the escaped URL): The name of the output file(s). Can also be a full path. Don't include an extension, as multiple files with different extensions will be generated.
  - `executablePath` (`String`) (optional) The full path to the OpenDirectoryDownloader executable.  
    Allows you to use a custom OpenDirectoryDownloader binary.
  - `workingDirectory` (`String`) (optional) The full path to the directory where OpenDirectoryDownloader saves its scan files.
  - `maximumMemory` (`Number`) (optional, default is `Infinity`) The maximum allowed memory usage in bytes for each scan.  
    If the limit is exceeded, the scan is aborted and rejects with an `ODDOutOfMemoryError`.
- Returns: An instance of `OpenDirectoryDownloader`

#### OpenDirectoryDownloader.scanUrl(url[, options])  

*Initiates the scan of the provided URL*

- `url` (`String` or `URL` Object) (required): The URL to scan/index.
- `options` (`Object`) (optional): Additional options for the scan
  - `outputFile` (`String`) (optional, defaults to the escaped URL): The name of the output file(s). Can also be a full path. Don't include an extension, as multiple files with different extensions will be generated.
  - `keepJsonFile` (`Boolean`) (optional, default is `false`): Keep the JSON-file generated by the OpenDirectoryDownloader binary after the scan?
  - `keepUrlFile` (`Boolean`) (optional, default is `false`): Keep the text-file generated by the OpenDirectoryDownloader binary after the scan?
  - `performSpeedtest` (`Boolean`) (optional, default is `false`): Perform a speed test after the scan is done? (usually takes a few seconds)
  - `uploadUrlFile` (`Boolean`) (optional, default is `false`): Automatically upload the file containing all the found URLs to GoFile?
  - `fastScan` (`Boolean`) (optional, default is `false`): Disable slow operations during the scan, like HEAD requests. This can result in file sizes being reported as "0" or "n/a", if the OD doesn't show file sizes next to the file names.
- `exactSizes` (`Boolean`) (optional, default is `false`] Use HEAD requests to retrieve exact file sizes
- `userAgent` (`String`) (optional, default is `""`] Use a custom user agent for all HTTP requests
- `auth` (`Object`) (optional) Used to configure (HTTP Basic) auth settings
  - `username` (`String`) (optional, default is `""`]) The user name to use for authentication
  - `password` (`String`) (optional, default is `""`]) The password to use for authentication
- `threads` (`Number`) (optional, default is `5`] Number of threads to use for scanning
- `timeout` (`Number`) (optional, default is `100`] Number of seconds to wait before timing out
- **Returns**: Promise<Resolves to `ScanResult` | Rejects to `Array<Error[,ScanResult]>`>  
  If the promise rejects, it will return an array where the first element is always an `Error` object and there might also be a second element, which is a `ScanResult` but without the `ScanResult.scan` property.

#### ScanResult

*The object returned when the promise resolves*

- `scannedUrl` The URL that was scanned
- `scan` The parsed JSON-Object created by the OpenDirectoryDownloader binary.  
  Can be very large depending on the size of the Open Directory.
- `jsonFile` The full path to the created JSON-file.  
  Only available if `keepJsonFile` was set to `true`.
- `urlFile` The full path to the created text-file.  
  Only available if `keepUrlFile` was set to `true`.
- `reddit` The markdown-formatted table containing the stats for the Open Directory.  
  Does not include the credits.
- `credits` The markdown signature containing a link to KoalaBear84/OpenDirectoryDownloader
- `missingFileSizes` If the `fastScan` option is enabled and the OD doesn't list file sizes, OpenDirectoryDownloader has no way of getting the file sizes, and this boolean will be set to `true`.

### Custom Errors

`open-directory-downloader` implements various custom errors to better indicate *what* went wrong. These errors can be used for more precise error handling, but also ignored and handled like a regular error.

#### ODDError

*An error indicating that the OpenDirectoryDownloader binary encountered an error.  
Often indicates that the URL couldn't be scanned, because it isn't supported.*

#### ODDOutOfMemoryError

*An error indicating that the OpenDirectoryDownloader process used more memory than allowed by `options.maximumMemory`.*

#### ODDWrapperError

*An error indicating that the wrapper itself encountered an error.  
This could be related to e.g. file system issues (deleted files, permission issues, etc.).*

## Examples

### Custom Scan Options

```js
const odd = require(`open-directory-downloader`);

const indexer = new odd.OpenDirectoryDownloader();

indexer.scanUrl(url, {
  outputFile: `/path/to/filename/without/extension`,
  keepJsonFile: true,
  keepUrlFile: true,
  performSpeedtest: true,
  uploadUrlFile: true,
})
.then(scanResult => {
  console.log(scanResult)
})
.catch(console.error)
```

### Error Handling

```js
const odd = require(`open-directory-downloader`);

const indexer = new odd.OpenDirectoryDownloader();

indexer.scanUrl(url)
.catch(err => {
  if (err instanceof odd.ODDError) {
    console.log(err.name) // 'ODDError'
  } else if (err instanceof odd.ODDWrapperError) {
    console.log(err.name) // 'WrapperError'
  }
})
```
