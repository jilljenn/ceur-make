ceur-make
=========

A free set of scripts to semi-automatically generate open access workshop proceedings for [CEUR-WS.org] [1], with special support for proceedings exported from [EasyChair] [2].

Key Advantages
--------------

* facilitates the generation of CEUR-WS.org workshop proceedings volumes
  * … particularly so for workshops using the EasyChair submission system
* enriches your CEUR-WS.org volume, as an extra feature over the standard template, with RDFa annotations to improve the visibility of your workshop to semantic search engines

Features
--------

* From a special table of contents file, generate
  * [a CEUR-WS.org compliant index.html file] [3] with additional RDFa annotations
  * [a CEUR-WS.org compliant copyright form] [4]
  * a LaTeX table of contents that helps you generate an all-in-one PDF version of the proceedings
  * a BibTeX database to make your proceedings citable
* Optionally generate this table of contents from [EasyChair] [2] proceedings
  
Disclaimer
----------

CEUR-WS.org may revise their [submission rules](http://ceur-ws.org/HOWTOSUBMIT.html) and their requirements for [index.html] [3] files, [copyright forms] [4], etc., at any time.  You are responsible for checking whether CEUR-WS.org have released new versions of these (concretely: if the CEURVERSION in the source of the [index.html] [3] template is the same as the one in [the source of toc2ceurindex.xsl](toc2ceurindex.xsl)).  The ceur-make developers welcome any [bug reports] [5] related to this.  If you manually edit the output generated by ceur-make, you will be doing so entirely at your own risk.  In particular, take care not to break any `CEUR...` annotations.

Use ceur-make at your own risk.  At the time of this writing, the documentation (both in this README file and in the sources) is not yet complete, but we will be working on this.

Prerequisites
-------------

* [GNU make](http://www.gnu.org/software/make/) (any recent version should be sufficient)
* [GNU bash](http://www.gnu.org/software/bash/) (any recent version should be sufficient)
* [Saxon-HE 9](http://saxon.sourceforge.net) (other XSLT 2 processors might work as well, but the Makefile currently assumes Saxon, and ceur-make has been tested with Saxon-HE 9.5)
* Optional:
  * [Perl 5](http://www.perl.org/) (for processing EasyChair proceedings; any recent version should be sufficient)
  * TeX (for generating an all-in-one proceedings file; any recent version should be sufficient; tested with [TeX Live 2012](http://www.tug.org/texlive/))
  
On a recent Linux all of these components (except maybe Saxon) should be installable via the central package manager.  On Mac OS, most components should be installable via [MacPorts](http://www.macports.org/) or [Fink](http://fink.thetis.ig42.org/), on Windows via [Cygwin](http://cygwin.com/).

ceur-make has so far been tested on Linux and Windows (using Cygwin); [reports] [5] from users of other systems are welcome.

How to use
----------

### Getting started ###

To get started, you need to copy the ceur-make scripts into the directory in which you would like to keep your proceedings.  You can do this by calling `./ceur-make-init path/to/your/directory` from the directory where you installed ceur-make.  Copy `Makefile.vars.template` to `Makefile.vars` and adapt the paths to point to your local installations of Saxon, etc.  (`ceur-make-init` doesn't do this automatically to prevent problems.)

### Export from EasyChair (optional) ###

When you use [EasyChair] [2] and instruct it to create an LNCS proceedings volume, ceur-make can automatically generate the XML table of contents from the EasyChair volume information.  Note that, for the purpose of ceur-make, LNCS just means that EasyChair will provide the proceedings for download in a ZIP file with a certain structure.  It doesn't mean that your proceedings will be published with Springer, nor that the papers have to be in the LNCS layout.

1. When creating a proceedings menu in EasyChair, use “9999” for the volume number (as this is currently hard-coded in ceur-make).
2. Download the final proceedings as a ZIP file and unzip it into a directory.
3. Copy the ceur-make scripts into that directory, so that they become siblings of the 9999PPPP per-paper directories, the README file, etc.
4. Generate toc.xml by `make toc.xml` and adapt it manually.  (If `make toc.xml` doesn't do its job, try to enforce it with `make -B toc.xml`.)
   * related issues: [#1](https://github.com/ceurws/ceur-make/issues/1)

#### Manually adapting toc.xml ####

If you would like to publish an all-in-one PDF it makes sense to use page numbers; otherwise it doesn't.  Therefore …

1. If you have an all-in-one PDF, please make sure you adapt the `pages` entries.  They will have to be shifted as soon as the PDF includes material before the papers, such as a preface, and if this material uses the same counter as the papers' pages, e.g. if the preface doesn't use Roman numerals.
2. If you do not generate an all-in-one PDF, please remove all `pages` entries.

### Generating CEUR-WS.org proceedings ###

To get started with this, you need a `toc.xml` file ([see this example](toc.xml)), which you can either write manually, or have generated from an EasyChair archive (see above).  Additionally, you need to write `workshop.xml` ([see this example](workshop.xml)) manually.

From these files, you can generate the following building blocks of a CEUR-WS.org proceedings volume.

* [the index.html file] [3] (via `make`, or specifically `make ceur-ws/index.html`)
* [the copyright form] [4] (via `make`, or specifically `make copyright-form.txt`)
* a LaTeX table of contents to help with generating an all-in-one PDF version of the proceedings (via `make`, or specifically `make toc.tex`)
* a BibTeX database (via `make`, or specifically `make ceur-ws/temp.bib`).  This file will need manual post-processing; please read on below.
* a ZIP archive for upload to CEUR-WS.org (via `make zip`)

#### Manually adapting index.html ####

If your editors have FOAF profiles, please consider manually adding `resource="foaf-profile"` in addition to `href="homepage"` for each editor.

If your authors have FOAF profiles, manually add `resource="foaf-profile"` to each outer `<span rel="dcterms:creator">`; otherwise, if they have homepages and you want to link to them, manually add `rel="foaf:homepage" resource="homepage"` to each inner `<span property="foaf:name">`.

#### Manually adapting the BibTeX database ####

The BibTeX database, generated as `ceur-ws/temp.bib` may work out of the box with [BibLaTeX](http://www.ctan.org/tex-archive/help/Catalogue/entries/biblatex.html) and [Biber](http://biblatex-biber.sourceforge.net/) but usually requires manual revision, as ceur-make does not handle persons' names as intelligently as BibTeX, and as `bibtex` does not support Unicode names and identifiers.

For these reasons we force you to manually inspect and revise `ceur-ws/temp.bib` and copy it to `ceur-ws/yourworkshopYYYY.bib` (according to the settings in `workshop.xml`); `ceur-ws/temp.bib` is not included in the ZIP archive for upload to CEUR-WS.org.

While you are working on `ceur-ws/temp.bib`, you can test it with `make bibtest.pdf` (which currently assumes plain old BibTeX, not Biber).

Contributors
------------

* [Christoph Lange](http://langec.wordpress.com)
* [Friederike Klan](http://fusion.cs.uni-jena.de/professur/about-us/team/friederike-klan)
* [Alexandre Rademaker](https://github.com/arademaker)

License
-------

This code is licensed under [GPL version 3](LICENSE) or any later version.

 [1]: http://ceur-ws.org "CEUR-WS.org"
 [2]: http://easychair.org "EasyChair"
 [3]: http://ceur-ws.org/Vol-XXX/index.html "index.html"
 [4]: http://ceur-ws.org/Non-Ex-Publication-Permission-Template.txt "copyright form"
 [5]: https://github.com/ceurws/ceur-make/issues "issues"
