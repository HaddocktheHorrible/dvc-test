# DVC-Test

## Bug Description

---

This repo replicates an issue found in `dvc status -c`. Specifically, missing directories on the remote are only returned as a WARNING message; they are not reported "missing" in the output from `dvc status -c` itself. The expected result is to return a "missing" status for the missing directories. Understandably, the contents of the missing directory files would not be reported.

To reproduce, clone this repo. It includes a "dummy" local remote with missing directory artifacts. A call to `dvc status -c` should produce the following:

```txt
$ dvc status -c
WARNING: Some of the cache files do not exist neither locally nor on remote. Missing cache files:
name: None, md5: b2e83ea9a3ac371304359cd04cf76c68.dir
        deleted:            foo.txt
```

Similarly, `dvc pull` will fail as expected:

```txt
$ dvc pull
WARNING: Some of the cache files do not exist neither locally nor on remote. Missing cache files:
name: None, md5: b2e83ea9a3ac371304359cd04cf76c68.dir
WARNING: No file hash info found for 'C:\Users\edwar\git\dvc-test\data'. It won't be created.
1 file failed
ERROR: failed to pull data from the cloud - Checkout failed for following targets:
C:\Users\edwar\git\dvc-test\data
Is your cache up to date?
<https://error.dvc.org/missing-files>
Both can be checked out by running `dvc checkout` from command line.
```

## Debug Info

---

DVC 2.15.0 was installed from exe for windows. `dvc doctor` output:

```txt
$ dvc doctor
DVC version: 2.15.0 (exe)
---------------------------------
Platform: Python 3.8.10 on Windows-10-10.0.19043-SP0
Supports:
        azure (adlfs = 2022.4.0, knack = 0.9.0, azure-identity = 1.10.0),
        gdrive (pydrive2 = 1.10.1),
        gs (gcsfs = 2022.5.0),
        hdfs (fsspec = 2022.5.0, pyarrow = 8.0.0),
        webhdfs (fsspec = 2022.5.0),
        http (aiohttp = 3.8.1, aiohttp-retry = 2.4.6),
        https (aiohttp = 3.8.1, aiohttp-retry = 2.4.6),
        s3 (s3fs = 2022.5.0, boto3 = 1.21.21),
        ssh (sshfs = 2022.6.0),
        oss (ossfs = 2021.8.0),
        webdav (webdav4 = 0.9.7),
        webdavs (webdav4 = 0.9.7)
Cache types: hardlink
Cache directory: NTFS on C:\
Caches: local
Remotes: local, local
Workspace directory: NTFS on C:\
Repo: dvc, git
```
