---
weight: 566
linkTitle: "OOD Exercises"
Title: "Basic OOD exercises"
description: "Workshops and Training Material - 2026 - OOD basics"
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

{{< alert type="info" >}}
A typical use case: Upload Data, use Jupter App to set up code, debug software environment, and produce results to be downloaded.
{{< /alert >}}


## Penguin Dataset analysis using pandas
---

{{< alert type="info" >}}
A typical use case: Upload some data in CSV format, use Jupter App to set up analysis, debug software environment, and produce results to be downloaded.
{{< /alert >}}

We will be using a free Penguins dataset, publicly available here, to do some statistical analysis. 

 * First, download the  [Penguin dataset](https://raw.githubusercontent.com/allisonhorst/palmerpenguins/master/inst/extdata/penguins.csv) to your laptop. 

 * Log in to an OpenOnDemand instance we use for training. Navigate to __File Manager__, and uploade the __penguins.csv__ file. Put it right into the home directory.

 * In the OpenOnDemand, go to Interactive Apps and find __JupyterLab__ . Start a session on a CPU (no GPUs!) partition using 1-2 CPU cores and 1h of time. Connect to the session when it starts.
 
 * In the JupyterLab launcher, start a Python 3.12 (or whichever is the default kernel) notebook.
 
Now we are ready to import Python packages. We will need __pandas__ , __seaborn__ and plotting packages.

 * Type or copy the code below in  notebooks cell: 
 
{{< highlight python >}}
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
{{< /highlight >}}

 * Run the above cell. Did it succed?

> In case some import fails, depending on the software stack, either some Modules need to be added, or __pip install__ in a cell would be used to add missing packages

 * Try to fix the Python environment as per above note! Restart the kernel if prompted. Module loads may need re-launching the notebook.
 
Now that we (hopefully) managed to install the dependencies, lets load the data and check its sanity.
 * In a new cell, add the following code, and run the cell:

{{< highlight python >}}
# Load the data (adjust path below as needed)
penguins = pd.read_csv('~/penguins.csv')
print(penguins.head())
print(penguins.info())
print(penguins['species'].value_counts())
{{< /highlight >}}

Lets do our "data analysis" and produce some artifact (an image with a plot).

 * In a new cell, add the code below and run it

{{< highlight python >}}
# A visualization: Bill length vs depth by species
plt.figure(figsize=(8, 6))
sns.scatterplot(data=penguins, x='bill_length_mm', y='bill_depth_mm',
hue='species', style='species', s=60)
plt.title('Palmer Penguins: Bill Length vs Depth')
plt.savefig('penguin_scatter.png', dpi=150, bbox_inches='tight')
plt.show()
# Another view: Flipper length vs body mass
plt.figure(figsize=(8, 6))
sns.scatterplot(data=penguins, x='flipper_length_mm', y='body_mass_g',
hue='species', style='sex', s=60)
plt.title('Flipper Length vs Body Mass by Species and Sex')
plt.savefig('penguin_flipper.png', dpi=150, bbox_inches='tight')
plt.show()
{{< /highlight >}}

At this point, we should have generated two __.png__ files somewhere. Those represents our "results".

* Locate the "results" files as per above. Use the __FIleManager__ in another browser tab to Download it to your laptop.

* Terminate the JupyterLab session. 

> Is just closing the tab enough to terminate the job?

## Done


<!-- Changes and update:
* Last revision: Oct 20, 2026.
-->
