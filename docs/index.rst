manipulation, validation and exploration of pedigrees
=====================================================

Pedagree is a python module for manipulating pedigree files.
While the python module has several useful features,
most users will be intereseted in `pedagree` for its 
utility in finding discrepancies between the relationships specified
in `.ped` files and the relationships inferred from a 
corresponding `.vcf` file such as can occur from sample-swaps 
or pedigree misspecifications.

The code to do this is quite simple. Below, we check for pedigree
violations by looking at 5,000 sites (see :doc:`relatedness <relatedness>`
for more details on selection) and for sex discrepancies by looking at the
non-PA regions of the X chromosome where males should have very few HET calls.

.. code-block:: python

    from pedagree import Ped
    p = Ped('ceph1463.ped')
    ped_df = p.ped_check('ceph1463.vcf.gz', plot='ped-check.png')
    sex_df = p.sex_check('ceph1463.vcf.gz', plot='sex-check.png', cutoff=0.15)

This will create the images:

.. image:: _static/ped-check.png

where samples are placed in the plot according to their relationship inferred from
the genotypes in the VCF and colored by their expected relationship from the PED file.
Parent-child relations (which should have IBS0 of 0) have an outline to differentiate
them from sib-sib relations.

and

.. image:: _static/sex-check.png

From both of these cases, we can see that it doesn't look like there are any
sample mixups. See the docs here for an example of how a sample mixup appears.


Both of these commands also create pandas dataframes that can be saved to a file with:

.. code-block:: python

    ped_df.to_csv('ped-check.tsv', sep="\t", index=False)
    sex_df.to_csv('sex-check.tsv', sep="\t", index=False)

The `sex-check` file will look like::

    error   het_count   het_ratio   hom_alt_count   hom_ref_count   ped_sex predicted_sex   sample
    FALSE   27056   0.1263089096893163  86640   127565  male    male    NA12877
    FALSE   103578  0.7371260212360158  48197   92319   female  female  NA12878
    FALSE   99578   0.7035623949015785  50029   91505   female  female  NA12879
    FALSE   98933   0.6946225083727102  51852   90575   female  female  NA12880
    FALSE   97733   0.6820833856762001  51843   91443   female  female  NA12881
    FALSE   25568   0.11903775333001225 85114   129675  male    male    NA12882
    FALSE   25740   0.12030398489423158 82830   131128  male    male    NA12883
    FALSE   26380   0.12349609100697533 84861   128749  male    male    NA12884
    FALSE   98882   0.68974609375   51608   91752   female  female  NA12885
    FALSE   27341   0.12795900219965367 85473   128197  male    male    NA12886
    FALSE   101838  0.7297442548709092  49321   90232   female  female  NA12887
    FALSE   24317   0.11512477334381199 84141   127082  male    male    NA12888
    FALSE   28980   0.13943418013856812 79538   128302  male    male    NA12889
    FALSE   100617  0.7108129874533741  48475   93077   female  female  NA12890
    FALSE   24443   0.11661792279543319 83839   125760  male    male    NA12891
    FALSE   100453  0.7119832162677459  47684   93405   female  female  NA12892
    FALSE   24774   0.11745685567987862 84482   126438  male    male    NA12893

And we can see that there are no inferred errors along with other information used to make that inference.

The `ped-check` file will contain `n_samples` \* `n_samples` number of rows like below::

    ibs0	ibs2	ibs2*	n	rel	sample_a	sample_b	pedigree_relatedness	pedigree_parents	predicted_parents	parent_error
    0.0801	0.0042	0.0165	4722	0.0245	NA12877	NA12878	0.0000	False	False	False
    0.0000	0.0125	0.0360	4723	0.5401	NA12877	NA12879	0.5000	True	True	False
    0.0000	0.0108	0.0390	4723	0.5482	NA12877	NA12880	0.5000	True	True	False
    0.0000	0.0119	0.0407	4723	0.5770	NA12877	NA12881	0.5000	True	True	False
    0.0000	0.0108	0.0351	4723	0.5448	NA12877	NA12882	0.5000	True	True	False
    0.0000	0.0112	0.0392	4723	0.5438	NA12877	NA12883	0.5000	True	True	False
    0.0000	0.0114	0.0379	4723	0.5452	NA12877	NA12884	0.5000	True	True	False
    0.0000	0.0110	0.0358	4723	0.5341	NA12877	NA12885	0.5000	True	True	False
    0.0000	0.0114	0.0390	4723	0.5597	NA12877	NA12886	0.5000	True	True	False

Using `IBS0`, we can infer the parent-child relationship of a pair of samples. If the inferred value
doesn't match the relationship in the ped file, then the `parent_error` column will be `True`.
We can see that the first line above has a high `ibs0` value (and low `rel` value) indicating it's
not expected to be a parent-child relation while the rest have low `ibs0` (and high `rel` values).


..
    .. toctree::
       :maxdepth: 2
..
    Indices and tables
    ==================
..
    * :ref:`genindex`
    * :ref:`modindex`
    * :ref:`search`
