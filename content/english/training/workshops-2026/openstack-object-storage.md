---
weight: 1500
linkTitle: "Openstack Object Storage - Arbutus"
Title: "Object storage example - Potree"
description: "Workshops and Training Material - 2026 - Object storage"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
#draft: true
#build:
# list: false
# render: false
---

## Introduction
---

Walkthrough to create a static website using Arbutus cloud object storage.

This tutorial assumes you already have access to an Arbutus cloud project and you have installed
 - the [python openstack client](https://pypi.org/project/python-openstackclient/)
 - the [s3cmd tool](https://s3tools.org/s3cmd)
 - [NodeJS](https://nodejs.org/)
 - [Git](https://git-scm.com/)

All the required software is available on Grex as modules.

Some useful links:
 - Arbutus (legacy) - [https://arbutus.cloud.computecanada.ca/](https://arbutus.cloud.computecanada.ca/)
 - Cloud on DRAC Wiki - [https://docs.alliancecan.ca/wiki/Cloud](https://docs.alliancecan.ca/wiki/Cloud)
 - Cloud Object Storage on DRAC Wiki - [https://docs.alliancecan.ca/wiki/Arbutus_object_storage](https://docs.alliancecan.ca/wiki/Arbutus_object_storage)

## Deploy a Potree website
---

1. Load required modules

{{< highlight bash >}}
module load git nodejs openstack-client s3cmd
{{< /highlight >}}

2. Download the `openrc` file from Arbutus dashboard

3. Create credential for the object storage

{{< highlight bash >}}
source def-training-cloud-openrc.sh
{{< /highlight >}}

{{< highlight bash >}}
openstack ec2 credentials create
{{< /highlight >}}

4. Configure `s3cmd` (using `object-arbutus.cloud.computecanada.ca` as endpoints)

{{< highlight bash >}}
s3cmd -c $HOME/.s3cmd_ws_2026 --configure
{{< /highlight >}}

5. Download and compile Potree

{{< highlight bash >}}
git clone https://github.com/potree/potree.git
cd potree
npm install
{{< /highlight >}}

6. Create an `index.html`

{{< highlight bash >}}
vim index.html
{{< /highlight >}}

{{< highlight html >}}
<!DOCTYPE html>
<html>
<head><title>Potree Examples</title></head>
<body>
<ul>
<li><a href="examples/viewer.html">Basic viewer</a></li>
<li><a href="examples/lion.html">Lion</a></li>
</ul>
</body>
</html>
{{< /highlight >}}

7. Create `BUCKET_NAME` using Arbutus dashboard (enabling `Public Access`)

8. Upload files to the bucket

{{< highlight bash >}}
for i in index.html build/ examples/ libs/ pointclouds/ ; do
  s3cmd -c ${HOME}/.s3cmd_ws_2026 --no-mime-magic -M sync ${i} s3://BUCKET_NAME/${i}
done
{{< /highlight >}}

9. Navigate to [https://object-arbutus.cloud.computecanada.ca/BUCKET_NAME](https://object-arbutus.cloud.computecanada.ca/BUCKET_NAME)

<!-- Changes and update:
* Last revision: May 21, 2026.
-->

