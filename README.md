In Silico Vaccinology Workshop on SARS-CoV-2
================

## Introduction

Using *in silico vaccinology* we can identify peptides which are
candidates for creating a vaccine.

I have created the following exercise as a cookbook for identifying such
potential vaccine candidates. Needless to say experimental validation is
required, but using the below procedure, we reduce the test space from
hundreds or even thousands of peptides to “a couple of handfuls”.

In the following you retrieve and analyze Severe acute respiratory
syndrome coronavirus 2 (SARS-CoV-2) sequences from the NCBI Virus
database. These are real data from real cases. We will focus on northern
Italy and attempt to identify peptides, which could potentially be used
in a CD8+ T-cell based vaccine. For sake of simplicity, we will skip
identifying CD4+ epitopes. This would naturally also be necessary. Also,
for the same reason, we will limit our analysis to `HLA-A`.

## Exercise

### Retrieve HLA allele data

First we need to insights into the prevalent HLA haplotypes of Northern
Italy, we can do this using the `The Allele Frequency Net Database` as
follows

1.  Go to [The Allele Frequency Net Database
    (AFND)](http://www.allelefrequencies.net/)
2.  Click the `HLA` tab
3.  Under `HLA searching options`, click `HLA classical allele freq
    search`
4.  Under `Locus:`, click `All loci` and select `A`
5.  Click `Select specific populations`, which will open a pop-up window
6.  Scroll down, find `Italy North pop 3 (n=97)` and click the box to
    the left
7.  Click `OK`
8.  In the lower right corner, click `Search`
9.  Note down the 5 most prevalent HLA-A alleles based on the `Allele
    Frequency (in_decimals)` column

### Perform Functional Clustring Analysis

Nextly, we will look into identifying if some of the selected HLA-A
alleles have similar binding preference. If that is the case, we can
reduce our search space.

1.  Go to [The MHCcluster 2.0
    Server](http://www.cbs.dtu.dk/services/MHCcluster/)
2.  Under `Select Allele(s)`, select the alleles you identified in the
    `Retrieve HLA allele data` section
3.  Click `Submit` at the bottom of the page
4.  The results page contain an `MHC specificity tree` and an `MHC
    specificity heat-map`. If two alleles are functionally very close,
    i.e. they bind the same peptides, we only need to consider one of
    them
5.  If possible, reduce your set of HLA-alleles to the minimum set of
    alleles, only choosing one, if two alleles are functionally similar

### Retrieve sequence data

Now, that we have our target alleles for the population of Northern
Italy, we need some `SARS-CoV-2` sequence data. I.e. in order to
identify peptide candidate, we must have some virus protein data we can
scan for potential peptides binders to our set of target alleles:

1.  Go to [National Center for Biotechnology Information Virus
    site](https://www.ncbi.nlm.nih.gov/labs/virus/vssi/#/)
2.  Under `UPDATES` click the `protein` link
3.  In the menu to the left, click `Geographic Region +`
4.  Click the `>` to the right of `Europe`
5.  Scroll down and find `Italy`, tick the box
6.  Now, you can investigate the proteins, by clicking their `Accession`
7.  Select three proteins you want to investigate for HLA-binding
    peptides by ticking the box to the left of `Accession`
8.  Find the blue `Download` button and click it
9.  Select `Protein`
10. Click `Next`
11. Select `Download Selected Records`
12. Click `Next`
13. Select `Use default`
14. Click `Download`

### Make binding predictions

We now have the set of target alleles and we have identified some
proteins of interest. Using the `netMHCpan`-prediction server, we can
predict protein derived peptides, which will bind our target alleles:

1.  Go to the [NetMHCpan 4.1
    Server](http://www.cbs.dtu.dk/services/NetMHCpan-4.1/)
2.  Make sure the `Type of input` is `fasta`
3.  Then click `Choose file`
4.  Under `PEPTIDE LENGTH`, select `9mer peptides` and `10mer peptides`
5.  Under `Select Allele(s)`, select the alleles you identifid in the
    `Perform Functional Clustring Analysis` section
6.  Tick the `Sort by prediction score` box
7.  Tick the `Save predictions to XLS file` box
8.  Click the green `Submit` button
9.  The output page contain information on the predictions. Scroll all
    the way down to the bottom and briefly inspect the `Explain the
    output` page, by clicking it
10. Now, download the generated `NetMHCpan_out.xls` file
11. Open it in excel and sort descending on the `NB` column
12. Identify all the identified `Peptide` binders with more than one
    binder (The `NB`-column)
13. Manually create the following two files for use in the following
    section (Recall that binders have an `EL_Rank` less than or equal to
    2). Here you can use `notepad` or `textEdit` depending on your
    operating system. Just be sure, that you create flat files, i.e. no
    word of similar text-processor should be
used.

#### epitope\_sample\_data.txt (Replace the below “dummy” data with your own)

``` text
GQIVGGVYLL HLA-A02:01 HCV_gt1
YLYGVGSSIA HLA-A02:01 HCV_gt1
AIKWEYVVLL HLA-A02:01 HCV_gt1
YGMWPLLLLL HLA-A02:01 HCV_gt1
TLVFDITKLL HLA-A02:01 HCV_gt1
ASLLKVPYFV HLA-A02:01 HCV_gt1
ILLGPADGMV HLA-A02:01 HCV_gt1
RLLAPITAYA HLA-A02:01 HCV_gt1
LLSPRPISYL HLA-A02:01 HCV_gt1
VLNPSVAATL HLA-A02:01 HCV_gt1
YRLGAVQNEV HLA-A02:01 HCV_gt1
.... .... ....
GSWLRDVW HLA-B58:01 HCV_gt2
GAPPLRAW HLA-B58:01 HCV_gt2
RLLDLSSW HLA-B58:01 HCV_gt2
```

#### reference\_HLA\_allele\_frequency.txt (Replace the below “dummy” data with your own)

``` text
HLA-A02:01 0.1670
HLA-A23:01 0.1560
HLA-A33:03 0.0850
HLA-A74:01 0.0770
HLA-A02:02 0.0620
.... .....
```

### Select epitopes for vaccine

In the case, where we have two different peptides binding the same
allele, we only want to keep one of them. But how do we make that choice
systematically, so we can reduce our set of vaccine-peptides to the
minimum? We use the PepCover algorith as follows:

1.  Go to the [PopCover 1.0
    Server](http://www.cbs.dtu.dk/services/PopCover-1.0/)
2.  Click `Choose file` under the `Paste epitope sample data in the
    format into the field below:`-window and upload the
    `epitope_sample_data.txt` you created above
3.  Click `Choose file` under the `Paste a reference HLA allele
    frequency file in the format into the field below:`-window and
    upload the `reference_HLA_allele_frequency.txt` you created above
4.  Click `Submit`
5.  From the output page, find the selected epitopes, by looking for the
    `# EPI`-tag and note them down

### Check HLA-binding

Finally, let’s verify that the selected peptides do indeed bind the
alleles we selected:

1.  Go to the [NetMHCpan 4.1
    Server](http://www.cbs.dtu.dk/services/NetMHCpan-4.1/)
2.  Change `INPUT TYPE` to `Peptide`
3.  Paste in the 5 peptides you identified in the previous section in
    the paste window (Check the format by clicking the pink
    `PEPTIDE`-link)
4.  Under `Select Allele(s)`, select the reduced allele set from earler
5.  Scroll down and click the green `Submit` button
6.  Hopefully, your peptides are indeed binders?

### Check if peptides are known epitopes

1.  Go to [Immune Epitope Database and Analysis
    Resource](http://www.iedb.org/) site
2.  Find the `START YOUR SEARCH HERE`-box
3.  Inside that, find the `Epitope`-box
4.  Select `Linear Epitope`
5.  From your popCover output copy/paste each of the 5 peptides into the
    field
6.  Repeat for all 5 peptides and play around with the `Exact match`
    versus `Blast 90%`

## Wrap up

So, did you find any epitopes, which were already experimentally
verified?

I hope the above illustrates in light details how you can use *in silico
vaccinology* in vaccine-development.

A few important remarks - Whether or not a given peptide is an epitope
is determined by the T-cell repertoire which is regulated by central
tolerance, i.e. all epitopes are HLA-binders, but not all HLA-binders
are epitopes. Using computational methods we can with great accuracy
predict
HLA-binders.

<img src="README_files/figure-gfm/unnamed-chunk-6-1.png" style="display: block; margin: auto;" />
