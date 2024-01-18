+++
title = "How to sort semantic versions in bash?"
date = 2024-01-18
description = "Sort semantic versions in bash"
+++

Example of files: 

```bash
pom-3.2.1.10-RELEASE.xml
pom-3.2.1.8-RELEASE.xml
pom-3.2.1.9-RELEASE.xml
pom-3.2.3-RELEASE.xml
pom-3.2.4-RELEASE.xml
pom-3.3.0-RELEASE.xml
pom-3.3.1-RELEASE.xml
pom-3.3.10-RELEASE.xml
pom-3.3.9-RELEASE.xml
```

## Sort by versions:
We can use '**sort -V**'
```bash
➜  tmp git:(master) ✗ ls -1 *-RELEASE.xml | sort -V             
pom-3.2.1.8-RELEASE.xml
pom-3.2.1.9-RELEASE.xml
pom-3.2.1.10-RELEASE.xml
pom-3.2.3-RELEASE.xml
pom-3.2.4-RELEASE.xml
pom-3.3.0-RELEASE.xml
pom-3.3.1-RELEASE.xml
pom-3.3.9-RELEASE.xml
pom-3.3.10-RELEASE.xml
```
For reverse order we can use '**-r**'

```bash
➜  tmp git:(master) ✗ ls -1 *-RELEASE.xml | sort -Vr
pom-3.3.10-RELEASE.xml
pom-3.3.9-RELEASE.xml
pom-3.3.1-RELEASE.xml
pom-3.3.0-RELEASE.xml
pom-3.2.4-RELEASE.xml
pom-3.2.3-RELEASE.xml
pom-3.2.1.10-RELEASE.xml
pom-3.2.1.9-RELEASE.xml
pom-3.2.1.8-RELEASE.xml
```

To get last version file we can use '**tail -n 1**'

```bash
➜  tmp git:(master) ✗ ls -1 *-RELEASE.xml | sort -V | tail -n 1 
pom-3.3.10-RELEASE.xml
```

We can use that value in bash script:
```bash
#!/bin/bash
last_version=$(ls -1 *-RELEASE.xml | sort -V | tail -n 1)

echo "Version: $last_version"
```