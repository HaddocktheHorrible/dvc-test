# DVC-Test

## Bug Description

---

This repo replicates an issue seen in DVC VSCode extension v.0.3.2 on 6/14/2022 running on Windows. Two issues are seen:

1. Any changes made to `bar/bat.txt` are not shown in the Source Control sidebar unless the VSCode window is reloaded. This only appears to be true of nested files; changes to `foo.txt` are correctly reflected in the sidebar automatically.

2. In testing for (1), another issue was found: neither `foo.txt` nor `bar/bat.txt` can be checked out from the dvc source control sidebar. The DVC extension outputs the following:

```bash
[version: 0.3.2, 2022-06-15T05:05:10.462Z, pid: 22564] > dvc checkout foo.txt - INITIALIZED
[version: 0.3.2, 2022-06-15T05:05:11.326Z, pid: 22564] > dvc checkout foo.txt - FAILED with code 255 (890ms)
ERROR: Did you mean `git checkout foo.txt`?: 'foo.txt' does not exist as an output or a stage name in 'dvc.yaml': 'dvc.yaml' does not exist
```

Both can be checked out by running `dvc checkout` from command line.

P.S. To simplify debugging, the dvc cache file is included in git. Yes, you normally would not do this :)

## Debug Info

---

DVC 2.11.0 was installed from exe for windows. `dvc doctor` output:

```bash
DVC version: 2.11.0 (exe)
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
Remotes: local
Workspace directory: NTFS on C:\
Repo: dvc, git
```

DVC Extension Output on startup:

```bash
[version: 0.3.2, 2022-06-15T05:30:26.800Z, pid: 24672] > dvc --version - INITIALIZED
[version: 0.3.2, 2022-06-15T05:30:27.404Z, pid: 24672] > dvc --version - COMPLETED (623ms)
[version: 0.3.2, 2022-06-15T05:30:27.438Z, pid: 12416] > dvc list . --dvc-only -R --show-json - INITIALIZED
[version: 0.3.2, 2022-06-15T05:30:27.481Z, pid: 14424] > dvc exp show --show-json - INITIALIZED
[version: 0.3.2, 2022-06-15T05:30:28.443Z, pid: 12416] > dvc list . --dvc-only -R --show-json - COMPLETED (1024ms)
[version: 0.3.2, 2022-06-15T05:30:28.460Z, pid: 24888] > dvc status --show-json - INITIALIZED
[version: 0.3.2, 2022-06-15T05:30:28.673Z, pid: 14424] > dvc exp show --show-json - COMPLETED (1212ms)
[version: 0.3.2, 2022-06-15T05:30:28.696Z, pid: 20880] > dvc plots diff -o .dvc\tmp\plots --split --show-json - INITIALIZED
[version: 0.3.2, 2022-06-15T05:30:30.834Z, pid: 24888] > dvc status --show-json - COMPLETED (2390ms)
[version: 0.3.2, 2022-06-15T05:30:30.855Z, pid: 8220] > dvc diff --show-json - INITIALIZED
[version: 0.3.2, 2022-06-15T05:30:30.856Z, pid: 20880] > dvc plots diff -o .dvc\tmp\plots --split --show-json - COMPLETED (2180ms)
[version: 0.3.2, 2022-06-15T05:30:33.059Z, pid: 8220] > dvc diff --show-json - COMPLETED (2224ms)
```

Additional output after `foo.txt` is edited and saved (this correctly shows an update in the source control sidebar):

```bash

[version: 0.3.2, 2022-06-15T05:31:52.807Z, pid: 24252] > dvc status --show-json - INITIALIZED
[version: 0.3.2, 2022-06-15T05:31:53.673Z, pid: 24252] > dvc status --show-json - COMPLETED (885ms)
[version: 0.3.2, 2022-06-15T05:31:53.697Z, pid: 24588] > dvc diff --show-json - INITIALIZED
[version: 0.3.2, 2022-06-15T05:31:54.677Z, pid: 24588] > dvc diff --show-json - COMPLETED (1003ms)
```

No additional output after `bar/bat.txt` is edited and saved. The changes are shown by `dvc status` from the command line:

```bash
$ dvc status
bar\bat.txt.dvc:
        changed outs:
                modified:           bar\bat.txt
```
