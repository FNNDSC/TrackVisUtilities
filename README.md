__NOTOC__
= Some handy utilities for working with TrackVis (*.trk) files =

== t_info.py ==
'''Display information for one or multiple TrackVis files.'''

In the console, run the following:

 $> t_info.py TRACKFILE1

or

 $> t_info.py TRACKFILE1 TRACKFILE2 TRACKFILE3...

'''Example:'''

To get information on the file '''/tmp/nocc4.trk''', do the following:
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
<br>
<br>
<br>
<br>

== t_calc.py ==
'''Add or Subtract multiple TrackVis files.'''

=== Add ===

It is possible to add/merge different TrackVis files to one.

In the console, run the following to add TRACKFILE1 and TRACKFILE2 and write the result to TRACKFILE_RESULT:

 $> t_calc.py add -i TRACKFILE1 -i TRACKFILE2 -o TRACKFILE_RESULT

It also works with more than two TrackVis files.

 $> t_calc.py add -i TRACKFILE1 -i TRACKFILE2 -i TRACKFILE3 -i TRACKFILE4 -o TRACKFILE_RESULT

'''Example:'''

To add the RILF.trk and LILF.trk structures from two different TrackVis files, the following command can be used:

 $> t_calc.py add -i RILF.trk -i LILF.trk -o sum.trk

The screenshots show the results.

{| class="wikitable" valign="top"
| <img src="http://fnndsc.github.com/gfx/trkutils/RILF.png"><br>Tracks of the RILF structure segmented.
| <img src="http://fnndsc.github.com/gfx/trkutils/LILF.png"><br>Tracks of the LILF structure segmented.
| <img src="http://fnndsc.github.com/gfx/trkutils/sum.png"><br>After running t_calc.py, RILF and LILF are '''available in one .trk-file'''.
|}

=== Subtract ===

It is possible to subtract TrackVis files from each other.

In the console, run the following to subtract TRACKFILE2 from TRACKFILE1 and write the result to TRACKFILE_RESULT:

 $> t_calc.py sub -i TRACKFILE1 -i TRACKFILE2 -o TRACKFILE_RESULT

It also works with more than two TrackVis files. In this case TRACKFILE2, TRACKFILE3 and TRACKFILE4 get subtracted from TRACKFILE1. '''Please note that the order of the input files is very important! The 1st input file is the master where all other inputs get subtracted from.'''

 $> t_calc.py sub -i TRACKFILE1 -i TRACKFILE2 -i TRACKFILE3 -i TRACKFILE4 -o TRACKFILE_RESULT

'''Example:'''

To subtract the Corpus Callosum (CC.trk) from all fibers (COMPLETE.trk), the following command can be used:

 $> t_calc.py sub -i COMPLETE.trk -i CC.trk -o COMPLETE_NO_CC.trk

The screenshots show the results.

{| class="wikitable" valign="top"
| <img src="http://fnndsc.github.com/gfx/trkutils/Start.png"><br>All fibers.
| <img src="http://fnndsc.github.com/gfx/trkutils/Cc.png"><br>Tracks of the CC structure segmented.
| <img src="http://fnndsc.github.com/gfx/trkutils/Result.png"><br>After running t_calc.py, the CC structure fibers were removed from all fibers.
|}

''Note: This script runs multi-threaded but the subtraction is very computational expensive and can run longer than 10 minutes on large TrackVis files!''
<br>
<br>
<br>
<br>

== t_transform.py ==
'''Transform (translate and/or rotate) all fibers in a TrackVis file.'''

''This script multiplies all fibers in a *.trk-file with a 4x4 matrix to re-position them. Therefore, it is possible to rotate and translate the fibers. Every TrackVis file stores a 4x4 matrix which includes the image voxel size, origin etc. and associates the image space to patient space. By using this script, this existing matrix gets multiplied with the new matrix in addition to the re-positioning of the fibers.''

In the console, run the following to rep-position the fibers using a 4x4 MATRIX and write out a new TrackVis file.

 $> t_transform.py -i TRACKFILE1 -m MATRIX -o TRACKFILE_RESULT

'''Example:'''

We have an existing TrackVis file (complete.trk) and want to '''translate all fibers by the vector (100,100,100)'''. This means the result's fibers would all be moved in x-, y-, and z-direction by 100.

The '''matrix can be created using a text editor''' and saved as a temporary file. In our case (translation) it should look like that:

<pre>
$>cat /tmp/matrix.txt 
1 0 0 100
0 1 0 100
0 0 1 100
0 0 0 1
</pre>

Now, we can run the '''t_transform.py''' command with the input TrackVis file ('''complete.trk'''), the matrix file ('''/tmp/matrix.txt''') and the output file ('''/tmp/output.trk''').

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

{| class="wikitable" valign="top"
| <img src="http://fnndsc.github.com/gfx/trkutils/Transformbefore.png"><br>'''Before the transformation.''' The distance to the fixed pink sphere is small.
| <img src="http://fnndsc.github.com/gfx/trkutils/Transformafter.png"><br>'''After the transformation.''' The distance to the fixed pink sphere is larger even if the sphere did not move.
|}

''Note: This script runs multi-threaded and is very fast - even on large TrackVis files.''
