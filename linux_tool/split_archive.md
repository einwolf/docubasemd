# Split archives for transport

Command reference for splitting a file into smaller pieces using an archiver.

WinZip will extract a zip file created by Linux zip splitting.

## split and cat

There is no verification so checksum all the pieces.

`split -b1G archive.zip`
`cat archive.* > archive.zip`

## tar

Does not split archive. See split and cat.

`tar cvzf - files | split -b1G - archive.tar.gz.`

## zip

```bash
zip -Z store -r archive.zip files*
zip archive.zip --out split.zip -s 1g
```

```bash
zip -s0 split.zip --out archive.zip
unzip -d archive.zip
```

## 7zip

`p7z a -m0=Copy -v1G archive.7z files*`

`p7z x archive.7z`

## Winzip 22.0

The zip archive must be created before it can be split.

* Create zip (not zipx)
* Add from pc
* Save zip 
* In Settings tab, set split size to 700 mb.
* In Tools tab, Click Multi Part Zip File
* Save parts
