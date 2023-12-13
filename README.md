# poshCMS
Powershell-based configuration management tools

# Overview
poshCMS is a collection of tools for performing content management tasks on systems with limited software resources.

# Features
poshCMS functionality should include the following: 
1. Download project files from web-based filesystem.
2. Maintain directory structure of files from web-based filesystem.
3. Compare configuration item states with known configuration.

# Implementation
poshCMS expects a project configuration file, in CSV format, that contains a comprehensive list of files in the project directory structure. Each row in the configuration file corresponds to one file. The columns specify the URL, directory path, and cryptographic hash (e.g., md5 sum) of each file. Files without hashes are excluded from configuration auditing (e.g., for files not under configuration management). The user is responsible for maintaining the configuration file.

## Example `poshCMS` configuration file

Download the example file [here](https://github.com/matthewgiarra/poshCMS/blob/e8f1250f899ec969634e388e08df0c2e063c0e3f/resources/config.csv).

| URL | Path | Hash (md5) |
| --- | --- | --- |
| https://docs.google.com/document/d/1v8KMG0xYqCNGa5oZiL9seaO3111kAwyA | configuration_management/artifacts/001/system_spec.docx | fe04b5b4e0e97a17de5055378674fbdd |
| https://docs.google.com/spreadsheets/d/1x1ISIFU3Rn29gLZVaUMzCHtiMF02vgAG | configuration_management/artifacts/001/system_spec_crm.xlsx |  |
| https://drive.google.com/file/d/1KR28MXyotrffL3cPL48PwwERs8xnRjrH | configuration_management/artifacts/001/system_spec.json |  |
| https://docs.google.com/document/d/1iiOSjp4BJ1u_hlTC2VanbaShpBWz4rCA | configuration_management/artifacts/002/subsystem_spec.docx | 88b22c123e2235e9bcbfd3c37682111c |
| https://docs.google.com/spreadsheets/d/1SE325RHV_4iHDJOHhYnSTIimnnhu0wpM | configuration_management/artifacts/002/subsystem_spec_CRM.xlsx |  |
| https://drive.google.com/file/d/1m4qUDC4jooDfM54VMC-NnVv1WJ7x6qpI | configuration_management/artifacts/002/subsystem_spec.json |  |
| https://docs.google.com/document/d/18KGPs6g1lsD_z-h9iXkotiH01wbTZ5nc | configuration_management/artifacts/003/component_icd.docx | e1a089680b64fdedf4682542145bb242 |
| https://docs.google.com/spreadsheets/d/1zR3g5pphen72Dm1ja15RnKf7nWdVvtT_ | configuration_management/artifacts/003/component_icd_crm.xlsx |  |
| https://drive.google.com/file/d/1SkVqsC0lRbF_ueYZLLMlAt_dQrHg2-9B | configuration_management/artifacts/003/component_icd.json |  |

## Downloading files: `poshcms download`
Features 1 and 2 are executed via the positional argument `download`:

```bash
./poshcms download --cfg <config file> --dir <project directory>
```

### Behavior
`poshcms download` does the following:

1. Reads the file specified by `<config file>`
2. Creates a directory structure (under `<project directory>`) implied by the list of file paths specified in the `Path` column of `<config file>`
3. Downloads each file specified in the `URL` column of `<config file>`, and saves it to the path specified in the corresponding `Path` column.

Example:
```bash
./poshcms download --cfg config.csv --dir myProject
```

If `--cfg`, is not specified, `poshCMS` searches for a file called `config.csv` in the current directory. 

if `--dir` is not specified, `poshCMS` creates the directory structure within the current directory. 

Therefore, the following commands are all equivalent:

```bash
./poshcms download
./poshcms download --cfg ./config.csv
./poshcms download --dir .
./poshcms download --cfg ./config.csv --dir .
```

`poshcms download` exits if:
1. The file specified by `--cfg` is not available for reading
2. The path specified by `--dir` is not available for writing

## Configuration auditing: `poshcms audit`
Feature 3 ("Compare configuration item states with known configuration") is executed via the positional argument `audit`:

```bash
./poshcms audit --cfg <config file> --dir <project directory>
```

### Behavior
`poshcms audit` does the following:
1. Reads the file specified by `<config file>`
2. Searches for each file identified in the config file, at the path specified in the `path` column, whose "hash" column is not empty
3. Calculates the file's hash (e.g., md5 sum) if the file is available for reading
4. Compares the file's hash to the corresponding value in the `hash` column of the config file.
5. Prints the path of each inspected file and indicates whether its hash matches or differs from the value in the `hash` column, or that the file was not available for reading (e.g., file not found). 

Example:

```bash
./poshcms audit --cfg config.csv --dir myProject
```

If `--cfg`, is not specified, `poshCMS` searches for a file called `config.csv` in the current directory. 

if `--dir` is not specified, `poshCMS` searches for files relative to the current directory. 

Therefore, the following commands are all equivalent:

```bash
./poshcms audit
./poshcms audit --cfg ./config.csv
./poshcms audit --dir .
./poshcms audit --cfg ./config.csv --dir .
```

`poshcms audit` exits if:
1. The file specified by `--cfg` is not available for reading
2. The files in the directory specified by `--dir` are not available for reading

