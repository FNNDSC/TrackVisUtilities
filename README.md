Some handy utilities for working with TrackVis (*.trk) files
=== 

### t_info.py ###
<b>Display information for one or multiple TrackVis files.</b>

In the console, run the following:

    $> t_info.py TRACKFILE1

or

    $> t_info.py TRACKFILE1 TRACKFILE2 TRACKFILE3...

<b>Example:</b>

To get information on the file <b>/tmp/nocc4.trk</b>, do the following:
<pre>
$>t_info.py /tmp/nocc4.trk
Nov 07 16:44:42 shaka ./t_info.py[27028] FILE: /tmp/nocc4.trk
Nov 07 16:44:42 shaka ./t_info.py[27028]   TRACKVIS VERSION: 2
Nov 07 16:44:42 shaka ./t_info.py[27028]   NUMBER OF TRACKS: 81559
Nov 07 16:44:42 shaka ./t_info.py[27028]   DIMENSIONS: [112 112  84]
Nov 07 16:44:42 shaka ./t_info.py[27028]   SPACING: [ 2.32142854  2.32142854  2.        ]
Nov 07 16:44:42 shaka ./t_info.py[27028]   ORIGIN: [ 0.  0.  0.]
Nov 07 16:44:42 shaka ./t_info.py[27028]   NUMBER OF SCALARS: 0
Nov 07 16:44:42 shaka ./t_info.py[27028]   NUMBER OF PROPERTIES: 0
Nov 07 16:44:42 shaka ./t_info.py[27028]   VOX2RAS Matrix:
Nov 07 16:44:42 shaka ./t_info.py[27028]     [ -2.32142591e+00  -1.21119356e-05  -3.05109867e-03   1.28114685e+02]
Nov 07 16:44:42 shaka ./t_info.py[27028]     [ -1.21119356e-05  -2.32137418e+00   1.36800176e-02   1.29665146e+02]
Nov 07 16:44:42 shaka ./t_info.py[27028]     [ -3.54145374e-03   1.58785917e-02   1.99995089e+00  -3.45956192e+01]
Nov 07 16:44:42 shaka ./t_info.py[27028]     [ 0.  0.  0.  1.]
Nov 07 16:44:42 shaka ./t_info.py[27028]   VOXEL ORDER: LPS
</pre>

### t_calc.py ###
<b>Add or Subtract multiple TrackVis files.</b>

It is possible to <b>add/merge</b> different TrackVis files to one.

In the console, run the following to add TRACKFILE1 and TRACKFILE2 and write the result to TRACKFILE_RESULT:

    $> t_calc.py add -i TRACKFILE1 -i TRACKFILE2 -o TRACKFILE_RESULT

It also works with more than two TrackVis files.

    $> t_calc.py add -i TRACKFILE1 -i TRACKFILE2 -i TRACKFILE3 -i TRACKFILE4 -o TRACKFILE_RESULT

<b>Example (Add):</b>

To add the RILF.trk and LILF.trk structures from two different TrackVis files, the following command can be used:

    $> t_calc.py add -i RILF.trk -i LILF.trk -o sum.trk

The screenshots show the results.


<img src="http://fnndsc.github.com/gfx/trkutils/RILF.png" width=250>&nbsp;&nbsp;&nbsp;
<img src="http://fnndsc.github.com/gfx/trkutils/LILF.png" width=250> &nbsp;&nbsp;&nbsp;
<img src="http://fnndsc.github.com/gfx/trkutils/Sum.png" width=250>
<br>Tracks of the LILF and RILF structures segmented individually. After running t_calc.py, RILF and LILF are <b>available in one .trk-file</b>.


It is possible to <b>subtract</b> TrackVis files from each other.

In the console, run the following to subtract TRACKFILE2 from TRACKFILE1 and write the result to TRACKFILE_RESULT:

    $> t_calc.py sub -i TRACKFILE1 -i TRACKFILE2 -o TRACKFILE_RESULT

It also works with more than two TrackVis files. In this case TRACKFILE2, TRACKFILE3 and TRACKFILE4 get subtracted from TRACKFILE1. '''Please note that the order of the input files is very important! The 1st input file is the master where all other inputs get subtracted from.'''

    $> t_calc.py sub -i TRACKFILE1 -i TRACKFILE2 -i TRACKFILE3 -i TRACKFILE4 -o TRACKFILE_RESULT

<b>Example (Subtract):</b>

To subtract the Corpus Callosum (CC.trk) from all fibers (COMPLETE.trk), the following command can be used:

    $> t_calc.py sub -i COMPLETE.trk -i CC.trk -o COMPLETE_NO_CC.trk

The screenshots show the results.

<img src="http://fnndsc.github.com/gfx/trkutils/Start.png" width=250> &nbsp;&nbsp;&nbsp;
<img src="http://fnndsc.github.com/gfx/trkutils/Cc.png" width=250>&nbsp;&nbsp;&nbsp;
<img src="http://fnndsc.github.com/gfx/trkutils/Result.png" width=250>

<br>Whole-brain fibers and tracks of the CC structure segmented. After running t_calc.py, the CC structure fibers were <b>removed from all fibers</b>.

<i>Note: This script runs multi-threaded but the subtraction is very computational expensive and can run longer than 10 minutes on large TrackVis files!</i>


### t_transform.py ###
<b>Transform (translate and/or rotate) all fibers in a TrackVis file.</b>

<i>This script multiplies all fibers in a *.trk-file with a 4x4 matrix to re-position them. Therefore, it is possible to rotate and translate the fibers. Every TrackVis file stores a 4x4 matrix which includes the image voxel size, origin etc. and associates the image space to patient space. By using this script, this existing matrix gets multiplied with the new matrix in addition to the re-positioning of the fibers.</i>

In the console, run the following to rep-position the fibers using a 4x4 MATRIX and write out a new TrackVis file.

    $> t_transform.py -i TRACKFILE1 -m MATRIX -o TRACKFILE_RESULT

<b>Example:</b>

We have an existing TrackVis file (complete.trk) and want to <b>translate all fibers by the vector (100,100,100)</b>. This means the result's fibers would all be moved in x-, y-, and z-direction by 100.

The <b>matrix can be created using a text editor</b> and saved as a temporary file. In our case (translation) it should look like that:

<pre>
$>cat /tmp/matrix.txt 
1 0 0 100
0 1 0 100
0 0 1 100
0 0 0 1
</pre>

Now, we can run the <b>t_transform.py</b> command with the input TrackVis file (<b>complete.trk</b>), the matrix file (<b>/tmp/matrix.txt</b>) and the output file (<b>/tmp/output.trk</b>).

<pre>
$>t_transform.py -i complete.trk -m /tmp/matrix.txt -o /tmp/output.trk
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Loading complete.trk..
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Old transformation matrix:
Nov 11 14:16:41 ipmi ./t_transform.py[5291]     [ -2.32142591e+00  -1.21119356e-05  -3.05109867e-03   1.28114685e+02]
Nov 11 14:16:41 ipmi ./t_transform.py[5291]     [ -1.21119356e-05  -2.32137418e+00   1.36800176e-02   1.29665146e+02]
Nov 11 14:16:41 ipmi ./t_transform.py[5291]     [ -3.54145374e-03   1.58785917e-02   1.99995089e+00  -3.45956192e+01]
Nov 11 14:16:41 ipmi ./t_transform.py[5291]     [ 0.  0.  0.  1.]
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Splitting the input into 8 pieces..
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Starting Thread-1...
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Starting Thread-2...
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Starting Thread-3...
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Starting Thread-4...
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Starting Thread-5...
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Starting Thread-6...
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Starting Thread-7...
Nov 11 14:16:41 ipmi ./t_transform.py[5291] Starting Thread-8...
Nov 11 14:16:44 ipmi ./t_transform.py[5291] All Threads done!
Nov 11 14:16:44 ipmi ./t_transform.py[5291] Merging output..
Nov 11 14:16:45 ipmi ./t_transform.py[5291] Merging done!
Nov 11 14:16:45 ipmi ./t_transform.py[5291] New transformation matrix:
Nov 11 14:16:45 ipmi ./t_transform.py[5291]     [ -2.32142591e+00  -1.21119356e-05  -3.05109867e-03  -1.04334227e+02]
Nov 11 14:16:45 ipmi ./t_transform.py[5291]     [ -1.21119356e-05  -2.32137418e+00   1.36800176e-02  -1.01105481e+02]
Nov 11 14:16:45 ipmi ./t_transform.py[5291]     [ -3.54145374e-03   1.58785917e-02   1.99995089e+00   1.66633183e+02]
Nov 11 14:16:45 ipmi ./t_transform.py[5291]     [ 0.  0.  0.  1.]
Nov 11 14:16:45 ipmi ./t_transform.py[5291] Saving /tmp/output.trk..
Nov 11 14:16:45 ipmi ./t_transform.py[5291] All done!
</pre>

As a result, all tracks were moved. The screenshots show the re-positioning in TrackVis.

<img src="http://fnndsc.github.com/gfx/trkutils/Transformbefore.png" width=300>&nbsp;&nbsp;&nbsp;
<img src="http://fnndsc.github.com/gfx/trkutils/Transformafter.png" width=300>
<br><b>Before the transformation:</b> The distance to the fixed pink sphere is small. <b>After the transformation:</b> The distance to the fixed pink sphere is larger even if the sphere did not move.

<i>Note: This script runs multi-threaded and is very fast - even on large TrackVis files.</i>


### License ###
Copyright (c) 2012 Fetal Neonatal Neuroimaging and Developmental Science Center, Children's Hospital Boston

This code is licensed under the MIT License: http://www.opensource.org/licenses/mit-license.php

<a href="http://childrenshospital.org/FNNDSC"><img src="http://xtk.github.com/chb_logo.jpg" alt="Children's Hospital Boston" title="Children's Hospital Boston"></a>
<a href="http://hms.harvard.edu"><img src="http://xtk.github.com/hms_logo.jpg" alt="Harvard Medical School" title="Harvard Medical School"></a>