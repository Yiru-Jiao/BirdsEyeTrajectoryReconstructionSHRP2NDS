This guide is generated (and verified) by ChatGPT. You are also recommended to inquire about an AI tool if any new problems arise.

## Problem

Opening the DOI or Dataverse page in a browser may show:

```text
Service temporarily unavailable
```

The author suggested trying Chrome Incognito mode first. However, some users reported that Incognito mode and even VPN access may still fail, while downloading through the Dataverse Data Access API works. See the discussion in [Issue #6](https://github.com/Yiru-Jiao/BirdsEyeTrajectoryReconstructionSHRP2NDS/issues/6#issuecomment-4159791792).

## Important: do not use the dataset-level zip API for this dataset

The dataset-level API endpoint may return an incomplete bundle:

```text
https://dataverse.vtti.vt.edu/api/access/dataset/:persistentId/?persistentId=doi:10.15787/VTT1/T7UUC1
```

In that case, `MANIFEST.TXT` may contain messages like:

```text
OverallDataDictionary_T7UUC1.pdf (application/pdf) 178241 bytes.
T7UUC1_Bird'sEyeView_Dataset.zip.001 (application/octet-stream) skipped because the total size of the download bundle exceeded the limit of 104857600 bytes.
T7UUC1_Bird'sEyeView_Dataset.zip.002 (application/octet-stream) skipped because the total size of the download bundle exceeded the limit of 104857600 bytes.
T7UUC1_Bird'sEyeView_Dataset.zip.003 (application/octet-stream) skipped because the total size of the download bundle exceeded the limit of 104857600 bytes.
T7UUC1_Bird'sEyeView_Dataset.zip.004 (application/octet-stream) skipped because the total size of the download bundle exceeded the limit of 104857600 bytes.
T7UUC1_Bird'sEyeView_Dataset.zip.005 (application/octet-stream) skipped because the total size of the download bundle exceeded the limit of 104857600 bytes.
```

`104857600 bytes` is 100 MiB. This means the dataset is accessible, but the large split zip files are skipped by the dataset-level bundle API.

Use the file-level API instead.

Expected files:

```text
OverallDataDictionary_T7UUC1.pdf
T7UUC1_Bird'sEyeView_Dataset.zip.001
T7UUC1_Bird'sEyeView_Dataset.zip.002
T7UUC1_Bird'sEyeView_Dataset.zip.003
T7UUC1_Bird'sEyeView_Dataset.zip.004
T7UUC1_Bird'sEyeView_Dataset.zip.005
```

---

# Windows PowerShell

Open **Windows PowerShell** and run the following commands.

```powershell
$SERVER = "https://dataverse.vtti.vt.edu"
$PERSISTENT_ID = "doi:10.15787/VTT1/T7UUC1"
$OUT = "$HOME\Downloads\T7UUC1"

New-Item -ItemType Directory -Force -Path $OUT | Out-Null
Set-Location $OUT

# 1. Download dataset metadata.
# Do not use $PID as a variable name because $PID is a built-in read-only PowerShell variable.
$encodedPid = [uri]::EscapeDataString($PERSISTENT_ID)
$metadataUrl = "$SERVER/api/datasets/:persistentId/?persistentId=$encodedPid"

curl.exe -L --fail --noproxy "*" -o "metadata.json" "$metadataUrl"

# 2. Parse metadata and list all files.
$json = Get-Content "metadata.json" -Raw | ConvertFrom-Json

$files = $json.data.latestVersion.files | ForEach-Object {
    [PSCustomObject]@{
        id       = $_.dataFile.id
        filename = $_.dataFile.filename
        size     = $_.dataFile.filesize
    }
}

$files | Sort-Object filename | Format-Table -AutoSize

# 3. Select the data dictionary PDF and all split zip parts.
$targetFiles = $files | Where-Object {
    $_.filename -eq "OverallDataDictionary_T7UUC1.pdf" -or
    $_.filename -like "T7UUC1_Bird'sEyeView_Dataset.zip.*"
} | Sort-Object filename

# 4. Download each file individually through the file-level Data Access API.
foreach ($f in $targetFiles) {
    Write-Host "Downloading $($f.filename) ..."
    $url = "$SERVER/api/access/datafile/$($f.id)"
    curl.exe -L --fail --retry 5 --retry-delay 5 --noproxy "*" -C - -o "$($f.filename)" "$url"
}

# 5. Verify downloaded files.
Get-ChildItem | Sort-Object Name | Format-Table Name, Length
```

## Extract on Windows

Install 7-Zip if needed:

```text
https://www.7-zip.org/
```

Then run:

```powershell
& "C:\Program Files\7-Zip\7z.exe" x "T7UUC1_Bird'sEyeView_Dataset.zip.001"
```

If 7-Zip is installed somewhere else, adjust the path to `7z.exe`.

Do **not** extract `.zip.002`, `.zip.003`, `.zip.004`, or `.zip.005` separately. Extract from `.zip.001` only.

---

# macOS

Install dependencies:

```bash
brew install jq sevenzip
```

Download files:

```bash
SERVER="https://dataverse.vtti.vt.edu"
PERSISTENT_ID="doi:10.15787/VTT1/T7UUC1"
OUT="$HOME/Downloads/T7UUC1"

mkdir -p "$OUT"
cd "$OUT" || exit 1

# 1. Download dataset metadata.
ENCODED_PID="$(python3 -c 'import urllib.parse; print(urllib.parse.quote("doi:10.15787/VTT1/T7UUC1", safe=""))')"

curl -L --fail \
  -o metadata.json \
  "$SERVER/api/datasets/:persistentId/?persistentId=$ENCODED_PID"

# 2. Show all files: id, filename, size.
jq -r '.data.latestVersion.files[] | [.dataFile.id, .dataFile.filename, .dataFile.filesize] | @tsv' metadata.json

# 3. Download the data dictionary PDF and all split zip parts.
jq -r '
  .data.latestVersion.files[]
  | select(
      .dataFile.filename == "OverallDataDictionary_T7UUC1.pdf"
      or (.dataFile.filename | startswith("T7UUC1_Bird'\''sEyeView_Dataset.zip."))
    )
  | [.dataFile.id, .dataFile.filename]
  | @tsv
' metadata.json | sort -k2,2 | while IFS=$'\t' read -r id filename; do
  echo "Downloading $filename ..."
  curl -L --fail --retry 5 --retry-delay 5 -C - \
    -o "$filename" \
    "$SERVER/api/access/datafile/$id"
done

# 4. Verify downloaded files.
ls -lh
```

Extract:

```bash
7zz x "T7UUC1_Bird'sEyeView_Dataset.zip.001"
```

Do **not** extract `.zip.002` to `.zip.005` separately.

---

# Linux

Install dependencies on Ubuntu/Debian:

```bash
sudo apt update
sudo apt install -y curl jq p7zip-full python3
```

Download files:

```bash
SERVER="https://dataverse.vtti.vt.edu"
PERSISTENT_ID="doi:10.15787/VTT1/T7UUC1"
OUT="$HOME/Downloads/T7UUC1"

mkdir -p "$OUT"
cd "$OUT" || exit 1

# 1. Download dataset metadata.
ENCODED_PID="$(python3 -c 'import urllib.parse; print(urllib.parse.quote("doi:10.15787/VTT1/T7UUC1", safe=""))')"

curl -L --fail \
  -o metadata.json \
  "$SERVER/api/datasets/:persistentId/?persistentId=$ENCODED_PID"

# 2. Show all files: id, filename, size.
jq -r '.data.latestVersion.files[] | [.dataFile.id, .dataFile.filename, .dataFile.filesize] | @tsv' metadata.json

# 3. Download the data dictionary PDF and all split zip parts.
jq -r '
  .data.latestVersion.files[]
  | select(
      .dataFile.filename == "OverallDataDictionary_T7UUC1.pdf"
      or (.dataFile.filename | startswith("T7UUC1_Bird'\''sEyeView_Dataset.zip."))
    )
  | [.dataFile.id, .dataFile.filename]
  | @tsv
' metadata.json | sort -k2,2 | while IFS=$'\t' read -r id filename; do
  echo "Downloading $filename ..."
  curl -L --fail --retry 5 --retry-delay 5 -C - \
    -o "$filename" \
    "$SERVER/api/access/datafile/$id"
done

# 4. Verify downloaded files.
ls -lh
```

Extract:

```bash
7z x "T7UUC1_Bird'sEyeView_Dataset.zip.001"
```

Do **not** extract `.zip.002` to `.zip.005` separately.

---


# Check whether the split files are complete

After downloading, you should have:

```text
OverallDataDictionary_T7UUC1.pdf
T7UUC1_Bird'sEyeView_Dataset.zip.001
T7UUC1_Bird'sEyeView_Dataset.zip.002
T7UUC1_Bird'sEyeView_Dataset.zip.003
T7UUC1_Bird'sEyeView_Dataset.zip.004
T7UUC1_Bird'sEyeView_Dataset.zip.005
```

All five `.zip.00x` parts must be in the same folder before extraction.

# Dataset-use note

The public version may not include all raw/environmental variables from the complete DUL-controlled version. For publication or reuse, follow the dataset license and acknowledge the data source.
