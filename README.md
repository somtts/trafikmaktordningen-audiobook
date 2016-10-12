# trafikmaktordningen-audiobook
Original data, derived files, scripts and notes related to building Swedish tts from the audiobook.


original_data contains the audiobook and a pdf with the text, from http://planka.nu/2012/06/26/trafikmaktordningen-som-ljudbok/
The license of this material is "copyme": http://www.kopimi.com, equivalent to Creative Commoncs CC0 "No Rights Reserved" license.

tmo contains files derived from the original data.

---------
Extract text
pdftotext original_data/manus_för_inläsning.pdf tmo/tmo_original.txt

Text that was read but not in the original text file was added manually.
This includes chapter headings, that can be used to split the main text into nine parts corresponding to the nine soundfiles.

So all text corrections should be made in this file: tmo/tmo.txt

Run split script: 'perl scripts/splitMasterText.pl'. Output: tmo/txt/tmo_0{1-9}.txt

---------

Corrections
tmo/tmo.txt has been corrected manually in many ways to match spoken audio. Midsentence newlines have been removed, and sentence-final newlines have been added. There should be one sentence on each line.

----------

Split audio and text into sentences.

Using aeneas ( https://github.com/readbeyond/aeneas ) to locate sentence boundaries in the audio files.

Aeneas can be configured to shift boundaries. Boundary offset -0.200 seems to be the best setting, it moves every boundary back 200 ms. Without it, many sentences start too late. With it, there are a few cases where they instead start too early.

TODO: Try to automatically find cases where aeneas splits in midword..

run sentence alignment script: perl scripts/runSentenceAlignment.pl (should not take more than a minute or two). Output: tmo/syncmaps/tmo_0{1-9}_syncmap.{json,html}

Check quality of alignment:
google-chrome tmo/syncmaps/tmo_01_syncmap.html
Listen, correct boundary if needed, save, copy syncmap from ~/Downloads to tmo/syncmaps_tuned

Actually split audio and text files (and also converting mp3 to wav):

first time: mkdir corpus; mkdir corpus/txt; mkdir corpus/wav

python scripts/splitAudioByJson.py

sox gives a lot of warning messages:

~~~
978.120
988.840
Vill du köpa originalutgåvan av boken, kan du göra det på bland annat www.korpen-koloni.se.
corpus/txt/tmo_1099.txt
sox "original_data/Trafikmaktordningen ljudbok/Trafikmaktordningen - Kapitel 9.mp3" corpus/wav/tmo_1099.wav trim 16:18.120000 0:10.720000
sox WARN mp3: MAD lost sync
sox WARN mp3: MAD lost sync
sox WARN mp3: recoverable MAD error
~~~

But the output files seem to be ok.

Output: corpus/txt/tmo_{0001-1099}.txt and corpus/wav/tmo_{0001-1099}.wav