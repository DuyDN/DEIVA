# DEIVA: Interactive Visual Analysis of differential gene expression test results

## Table of contents

* [Interactive exploration of gene expression tests made easy](#interactive-exploration-of-gene-expression-tests-made-easy)
* [Features](#features)
* [Using the DEIVA web interface](#using-the-deiva-web-interface)
   * [User-defined custom colors for symbols](#user-defined-custom-colors-for-symbols)
   * [Features with more than one symbol assigned should be separated by semicolon to enable multi-find](#features-with-more-than-one-symbol-assigned-should-be-separated-by-semicolon-to-enable-multi-find)
* [DEIVA input file format](#deiva-input-file-format)
   * [DESeq2 flavor](#deseq2-flavor)
   * [edgeR flavor](#edger-flavor)
   * [Detailed example on generating DESeq2 and edgeR-based input files](#detailed-example-on-generating-deseq2-and-edger-based-input-files)
   * [The index file](#the-index-file)
* [Deploying DEIVA with your own data](#deploying-deiva-with-your-own-data)
   * [Pre-built](#pre-built)
   * [Building from source](#building-from-source)
* [Standalone](#standalone)
* [Shiny prototype](#shiny-prototype)
* [About Project χ](#about-project-χ)
* [Contact](#contact)
* [How to cite](#how-to-cite)
* [Acknowledgments](#acknowledgments)
* [Source Code License](#source-code-license)

## Interactive exploration of gene expression tests made easy

DEIVA (Differential Gene Expression Interactive Visual Analysis) is a web app to interactively identify and locate genes in a hexbin or scatter plot of DESeq2 or edgeR results.

The aim was to create a web app that meets user expectations and can be used without any knowledge of R, spreadsheets, or programming.

Using DEIVA, domain experts can examine the results of a differential gene expression test and immediately get answers to questions such as "What is the expression level of my genes of interest?", "How does expression of these genes differ between the two different states?", and can easily retrieve lists of genes just by brushing (highlighting) them.

While this could also be achieved using any spreadsheet program like Excel, LO Calc or Google Sheets, that process would be slow, cumbersome, and not intuitive.

DEIVA provides an interface where domain experts simply go to a URL and can immediately search for genes, retrieve genes, and filter results lists.

## Features

* **Select** an experiment from a drop-down list of datasets results.
* **Identify** genes by brushing.
* **Locate** genes by searching for their name. **Multiple genes can be located at the same time** (separated by a space), and are **automatically highlighted in different colors**
* **Search** for genes in the data table.
* **Zoom** using the toolbar to zoom into selected rectangle and to reset.
* **Filter** two different cut-off sliders which are linked by logical "and". Number of genes passing the filter, up and down, is shown.
* **Mobile device support**. The user interface adapts to desktop as well as mobile environment devices.
* **User data**.  Drag and drop formatted DGE files onto the plot area to view in DEIVA. (See [DEIVA input file format](#deiva-input-file-format))

## Using the DEIVA web interface

The interface is self-explanatory and should be usable by anyone accustomed to a contemporary web app. If you require further instructions use the "Show me" in the upper right of the user interface.

### User-defined custom colors for symbols

After searching and finding a symbol through the search box, the symbol appears as a label in the search box. Within this label, there is a counter which indicates how many features have been found with this symbol. The counter has the same color as the symbol in the plot and the legend. Clicking on counter opens a color chooser. This enables the user to define a custom color for any symbol. The colors are remembered for each symbol. For example, if the user finds a symbol, changes the color, deletes the symbol, and finally finds that symbol again, the last color assigned to this symbol is remembered.

### Features with more than one symbol assigned should be separated by semicolon to enable multi-find

Symbols can also be found by clicking the symbol name in the table. If a feature is associated with multiple symbols (separated by semicolons) all symbols are added to search box as separate labels. The color assigned to the first matching symbol in the search box is used for highlighting the respective feature. For example, a feature might be associated with the symbol "Kif1b;Cort". This means it belongs to the gene "Kif1b" as well as "Cort". When clicking on the symbol annotation "Kif1b;Cort" in the table, both gene symbols will be added to the search box as separate entities. Features annotated with "Kif1b" or "Cort" will be highlighted. Features annotated with multiple symbols including "Kif1b" or "Cort" (for example "Kif1b;Cort") will be highlighted separately in the same color as the first matching symbol in the search box.

## DEIVA input file format

DEIVA accepts input files in two different input formats. Both input file formats are simple ASCII files containing a certain number of columns, either all tab- or comma-separated. Additional columns can be added as the user sees fit and are included in the table and can, therefore, be searched and sorted by, but have no effect on plot rendering. We refer to the two possible input file formats as the "DESeq2 flavour" and the "edgeR flavour". This alludes to the fact that these input file formats can be generated most easily when the differential gene expression test has been done with DESeq2 or edgeR respectively. However, DEIVA is in no way specific to DESeq2 or edgeR; the input file formats of DEIVA are designed in a way to ease working with these two packages as we assume that DESeq2 and edgeR are the most popular packages for differential gene expression testing.

Please keep in mind that neither DESeq2 nor edgeR have an "output format" as such, the exact format in which you write the resulting tables to disk depends on which function in R you use.

The number of required columns are six for the DESeq2 flavor and seven for the edgeR flavor.

### DESeq2 flavor

The input file needs to have the following columns:

* feature
* symbol
* baseMean
* log2FoldChange
* pvalue
* padj

The header *does* contain an entry for the first column! Typically, when writing a data frame from R, containing the result of a DESeq2 analysis, a user might omit the first entry and keep the first column unnamed - however it is required in this application.

DESeq2 does not have a standard output file format. Write the result of a differential expression test to a file with TAB or COMMA as the separator and no hyphens to delineate fields.  The file should be named appropriately (.tsv for tab separated, .csv for comma separated).

The DESeq2 output is augmented by one column: `symbol`. This contains a symbol (or multiple symbols separated by semicolons) associated with the feature (cluster, transcription initiation site) of interest. These symbols are searched for when using the "Locate symbol" feature. The feature column can not be used for this, because there is no one-to-one relationship between features and symbols. Multiple features may be associated with the same symbol (or even list of symbols).

The `symbol` column will typically contain gene names or gene symbols associated with the respective feature, but can contain any annotation desired (cluster names, protein identifiers...).

Example for a DEIVA input file:

|	feature |symbol	|baseMean	|log2FoldChange		|lfcSE		|stat		|pvalue		|padj|
|---	|---	|---		|---			|---		|---		|---		|--- |
| chr2\_246405441\_246405521\_+	| Grid | 1.64173845899039 | 0.899050240043084 | 4.76263324936837 | 0.188771671671825 | 0.850271775363256 | 0.925548049366256 |
| chr12\_54086426\_54086466\_+	| Pcp2 | 5.30160933550825 | 1.61745325417192 | 4.82390235826869 | 0.335299749879769 | 0.737398982387807 | 0.864181693747462 |
| chr7\_36876604\_36876766\_+	| n/a | 1.18273903072599 | -6.06413567391466 | 3.87533117153996 | 1.564804504567 | 0.117628755226941 | 0.585133009869474 |

Note:
- The symbol column is optional.
- Columns may appear in any order.
- Multiple symbols in the symbol column must be separated by semicolons.

### edgeR flavor

An alternative input format is also possible, this is especially useful when starting from edgeR files. Here the input file needs to have the following columns:

* feature
* logFC
* logCPM
* LR
* PValue
* FDR
* symbol

Example:

| feature |logFC   |logCPM  |LR      |PValue  |FDR     |symbol|
|---	|---	|---		|---			|---		|---			|--- |
| chr10\_100486611\_100486680\_+     |-0.792817368178757      |6.15713415308089        |5.89498642469805        |0.0151840465950009      |0.0413077938423281      |Kcnj16|
| chr10\_102389850\_102389932\_+     |0.971318236831185       |3.51479142943153        |2.31041232774695        |0.128509956456949       |0.233565642765819       |NA|
| chr10\_102393454\_102393460\_+     |1.21869694330981        |3.74540666988435        |5.39366227820117        |0.020210011581406       |0.0523206275040398      |NA|

### Detailed example on generating DESeq2 and edgeR-based input files

See this git repository, describing how the example input files for DEIVA have been generated:

[antonkratz/genome-research-edgeR-DESeq2](https://github.com/antonkratz/genome-research-edgeR-DESeq2.git)

### The index file

In addition to the input file(s), it is necessary to provide an index file named `index.tsv`. This is a simple file starting with a header line (filename, name, gene) separated by tab, followed by one line per input file, with three fields per line, separated by tab: filename (name of the input filename), name (a string describing the entry for that filename in the dropdown) and gene (a space-separated list of genes highlighted by default when this filename is loaded).

As an example, here is what the `index.tsv` file looks like for the example data set in the live instance demo:

| filename        |name    |gene|
|---	|---	|---	|
| DESeq2.bound_vs_unbound.tsv     |Bound vs unbound (DESeq2)       |Pcp2 Cacna1g|
| edgeR.bound_vs_unbound.tsv      |Bound vs unbound (edgeR)        |Pcp2 Cacna1g|
| DESeq2.bmemb_vs_bcyto.tsv       |Bound membrane vs bound cytoplasm (DESeq2)      |Pcp2 Cacna1g|
| edgeR.bmemb_vs_bcyto.tsv        |Bound membrane vs bound cytoplasm (edgeR)       |Pcp2 Cacna1g|

# Deploying DEIVA with your own data

## Pre-built

DEIVA may be used with custom data without modification to the source code.  Download the [gh-pages branch Zip file](https://github.com/Hypercubed/DEIVA/archive/gh-pages.zip) and replace the files in the `data/` with your data.  (See [Preparing input data files](##preparing-input-data-files)).  You will also need to modify the `index.tsv` file to contain a list of additional data files available in the interface (see example [here](https://github.com/Hypercubed/DEIVA/tree/gh-pages/data)).

## Building from source

DEIVA was developed using the [Project χ toolkit](https://github.com/Hypercubed/Project-chi).  If you are not familiar with Project-χ please see [here](https://github.com/Hypercubed/Project-chi#readme).  To utilize Project χ You should be familiar with [JSPM](http://jspm.io/), [SystemJS](https://github.com/systemjs/systemjs), and [Gulp](http://gulpjs.com/).

```sh
git clone https://github.com/Hypercubed/Project-Chi.git
cd Project-Chi
git checkout tags/v1.0.0  # ensure you are using the same version of Project χ
npm install # jspm install is run post-install by npm
git clone https://github.com/Hypercubed/DEIVA.git dataset/DEIVA
```

Now add your data to the `./dataset/DEIVA/app/data` directory (see example [here](https://github.com/Hypercubed/DEIVA/tree/gh-pages/data)).  Alternatively you may download data from [antonkratz/genome-research-edgeR-DESeq2](https://github.com/antonkratz/genome-research-edgeR-DESeq2):

```sh
svn checkout https://github.com/antonkratz/genome-research-edgeR-DESeq2/trunk/annotated
mv annotated/* dataset/DEIVA/app/data/
rm -rf annotated/
```

Start the development server:

```sh
gulp dev --dataset=./dataset/DEIVA/ --bundle --open
# http://localhost:9000 will be opened in your default browser
```

# Standalone

Standalone versions of DEIVA can be found in the [releases](https://github.com/Hypercubed/DEIVA/releases). These have been generated with [ELECTRON](http://electron.atom.io). 

# Shiny prototype

A prototype of DEIVA has been implemented using R/Shiny: [Shiny prototype](https://github.com/antonkratz/deiva_github).

This prototype is a very minimal implementation which allows locating and identifying features. The code in that Git repository is under MIT license as well and can serve as a starting point for another direction of DEIVA, in particular the integration of DEIVA with the differential gene expression algorithm itself. This would be a framework which necessitates a running R server in the background.

# About Project χ

This website was built using the [Project χ platform](https://github.com/Hypercubed/Project-chi). Project χ (pronounced project kai or /<abbr title="/ˈ/ primary stress follows">ˈ</abbr><abbr title="'k' in 'kind'">k</abbr><abbr title="/iː/ long 'e' in 'bead'">iː</abbr>/) is a modular open-source toolkit for building web and electron data visualization applications built by Jayson Harshbarger at the [RIKEN Institute in Yokohama Japan](http://www.yokohama.riken.jp/english/).  It offers a template and toolset for building self-hosted data-centric visualization websites. Geared towards sharing of supplemental materials associated with scientific publications; Project χ allows visitors to interact with visualizations, download associated data and images, and even try the visualization with their own uploaded or publicly available datasets.  For developers the framework comes packaged with tools necessary for quickly integrating interactive visualizations using [d3.js](http://d3js.org/), [AngularJS](https://angularjs.org/), and [BioJS](http://biojs.io/). More information can be found [here](https://github.com/Hypercubed/Project-chi#readme).

# Contact

For more information please contact [J. Harshbarger](mailto:jayson.harshbarger@riken.jp)

## How to cite

TBD

## Acknowledgments

DEIVA visualization conceptualized and prototyped by Anton Kratz.

Project-χ implementation of DEIVA developed by Jayson  Harshbarger with inspiration from DESeq2IVA (Shiny version) and significant input from Anton Kratz.

This work was supported by a research grant from the Japanese Ministry of Education, Culture, Sports, Science and Technology (MEXT) to the RIKEN Center for Life Science Technologies.

## Source Code License

[MIT License](http://en.wikipedia.org/wiki/MIT_License)

Copyright (c) 2016 RIKEN, Japan.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
