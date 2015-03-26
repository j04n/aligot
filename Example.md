These are non-obfuscated examples to demonstrate the tool usage.

# TEA #

## Preliminary analysis ##

Let's take a look at **vanilla/testBinaries/TEA/TEA.exe**. You should rapidly found the following function that seems to do some magic stuff:

<img src='http://aligot.googlecode.com/svn/img/TEA_IDA.png' height='500'>

Let's check if it is a known crypto algorithm :)<br>
<br>
<h2>Tracing</h2>
<ul><li>Get the pintool <b>vanilla/tracer/pin2.12/aligotTracer.dll</b></li></ul>

<i>Note: Depending on your Pin version, this could fail at the execution. In this case you have to compile the tracer yourself (vanilla/tracer/src/).</i>

<ul><li>Collect the execution trace:</li></ul>

<hr />
<pre>
$ pin -t aligotTracer.dll startA 401020 endA 40109F -- TEA.exe<br>
Input text:<br>
123456789abcdef<br>
Key:<br>
deadbee1deadbee2deadbee3deadbee4<br>
Output text:<br>
df5ec1536e089494<br>
</pre>
<hr />

We specified the start and end addresses of the previous function with the arguments <b>startA</b> and <b>endA</b>.<br>
<br>
<i>Note: You could also collect a full trace of the program but the rest of the analysis is going to take a lot more time. Currently the tool is only suitable for such focus analysis.</i>

<ul><li>You should then obtain the file <b>trace.out</b> that looks like this:<br>
<hr />
<pre>
!SOT<br>
401020!push ebp!RR_ebp_12ff7c_esp_12ff5c!WM_12ff58_4_12ff7c!WR_esp_12ff58<br>
401021!mov ebp, esp!RR_esp_12ff58!WR_ebp_12ff58<br>
401023!sub esp, 0x14!RR_esp_12ff58!WR_esp_12ff44<br>
...<br>
</pre>
<hr />
<h2>Extraction of possible crypto algorithms</h2></li></ul>

<ul><li>Go into <b>vanilla/extraction</b> and launch:<br>
<hr />
<pre>
$ python main.py trace.out<br>
> Aligot extraction module<br>
> Start: 2012-09-27 15:03:32.489000<br>
---------------------------<br>
<br>
> Loop detection... Done<br>
> Garbage collector for invalid loops... Done (47 loops suppressed)<br>
> Loop I/O... Done<br>
> Garbage collector for useless loops (no I/O)... Done (0 loops suppressed)<br>
> Loop data flow graph building... Done<br>
> Garbage collector for invalid LDFs... Done<br>
> Loop data flow I/O building... Done<br>
> Garbage collector for useless LDFs... Done<br>
> Assigning values to I/O memory parameters... Done<br>
> Dumping results... Done<br>
> Producing graph... Done<br>
<br>
---------------------------<br>
> End: 2012-09-27 15:03:32.704000<br>
</pre>
<hr /></li></ul>

You should then obtain three files:<br>
<ul><li><b>finalGraph0.png</b>, a graph representing one possible crypto algorithm extracted from the trace:<br>
<img src='http://aligot.googlecode.com/svn/img/TEA_graph.png' width='650'>
<br />
We notice that the input text, output text, key (as named when executed) have been correctly collected, but they are separated within different parameters. The identification phase is going to recombine them together.<br>
</li><li><b>finalGraph0.dot</b>, same graph in dot format.<br>
</li><li><b>result.txt</b>, contains the input-output values for each extracted algorithm.</li></ul>

This last file is the one we are going to use in the next phase.<br>
<br>
<i>Note: For a better understanding of the tool, we can check:<br>
<ul><li>"--debug_graph" to produce another graph describing more precisely the loops extracted from the execution trace. In this case:<br></i><img src='http://aligot.googlecode.com/svn/img/TEA_dg.png' height='400'></li></ul>

<i>We clearly see our loop, with its arguments.</i>

<ul><li><i>"--debug_mode" to display every step of the loop detection algorithm.</i></li></ul>

<h2>Identification</h2>

<ul><li>Go into <b>vanilla/comparison</b> and launch:<br>
<hr />
<pre>
$ python main.py result.txt<br>
> Aligot identification module<br>
> Start: 2012-10-02 14:19:28.164000<br>
-----------------------------------<br>
<br>
> No particular ciphers selected, test with all of them : ['tea', 'xtea', 'russian_tea', 'rc4','aes_128_core']<br>
<br>
> Testing LDF 1 ...<br>
> Heuristic : Blacklisting classic values for TEA (disable it with --no-bl-heuristic)<br>
> Comparison with TEA...<br>
<br>
!! Identification successful: TEA decryption with:<br>
==> Encrypted text (8 bytes) : 0x0123456789abcdef<br>
==> Key (16 bytes) : 0xdeadbee1deadbee2deadbee3deadbee4<br>
==> Decrypted text (8 bytes) : 0xdf5ec1536e089494<br>
<br>
-----------------------------------<br>
> End: 2012-10-02 14:20:31.172000<br>
</pre>
<hr /></li></ul>

Win \o/<br>
<br>
<i>Note: If you are familiar with the TEA family ciphers, you should have noticed that <b>finalGraph0.png</b> contained some very TEA-specific constant value (0xc6ef3720). In this case you could directly test for TEA-like ciphers by giving to the script "--ciphers tea xtea russian_tea".</i>

<h1>RC4</h1>

<h2>Preliminary analysis</h2>

By looking at <b>vanilla/testBinaries/RC4/RC4.exe</b>, we can observe the following loops that seem to decrypt something:<br>
<br>
<img src='http://aligot.googlecode.com/svn/img/RC4_IDA_1.png' height='500'>
<img src='http://aligot.googlecode.com/svn/img/RC4_IDA_2.png' height='500'>

Let's check if it is a known crypto algorithm :)<br>
<br>
<h2>Tracing</h2>

We collect manually the corresponding start and end addresses, and launch the tracer:<br>
<br>
<hr />
<pre>
$ pin -t aligotTracer.dll startA 401060 endA 40159A -- RC4.exe<br>
Key:<br>
SuperKeyIsASuperKey<br>
Plaintext:<br>
SuperPlainMessageABaseDeTrompette<br>
Encrypted text:<br>
d3e852f160d60dd627d66860e97148f5<br>
</pre>
<hr />

<h2>Extraction of possible crypto algorithms</h2>

<ul><li>Go into <b>vanilla/extraction</b> and launch:<br>
<hr />
<pre>
$ python main.py trace.out<br>
> Aligot extraction module<br>
> Start: 2012-09-27 16:24:01.554000<br>
---------------------------<br>
<br>
> Loop detection... Done<br>
> Garbage collector for invalid loops... Done (70 loops suppressed)<br>
> Loop I/O... Done<br>
> Garbage collector for useless loops (no I/O)... Done (0 loops suppressed)<br>
> Loop data flow graph building... Done<br>
> Garbage collector for invalid LDFs... Done<br>
> Loop data flow I/O building... Done<br>
> Garbage collector for useless LDFs... Done<br>
> Assigning values to I/O memory parameters... Done<br>
> Dumping results... Done<br>
> Producing graph... Done<br>
<br>
---------------------------<br>
> End: 2012-09-27 16:24:02.611000<br>
</pre>
<hr /></li></ul>

At this point you should get <b>finalGraph0.png</b>, <b>finalGraph0.dot</b> and <b>result.txt</b>.<br>
<br>
<h2>Identification</h2>

<ul><li>Go into <b>vanilla/comparison</b> and launch:<br>
<hr />
<pre>
$ python main.py result.txt<br>
> Aligot identification module<br>
> Start: 2012-10-02 14:24:54.349000<br>
-----------------------------------<br>
<br>
> No particular ciphers selected, test with all of them : ['tea', 'xtea', 'russian_tea', 'rc4','aes_128_core']<br>
> Testing LDF 1 ...<br>
> Comparison with TEA... Fail!<br>
> Comparison with XTEA... Fail!<br>
> Comparison with RussianTEA... Fail!<br>
> Comparison with RC4...<br>
<br>
!! Identification successful: RC4 encryption with:<br>
==> Plain text (33 bytes) : 0x5375706572506c61696e4d6573736167654142617365446554726f6d7065747465<br>
==> Key (19 bytes) : 0x53757065724b657949734153757065724b6579<br>
==> Encrypted text (33 bytes) : 0xd3e852f160d60dd627d66860e97148f57a71ada22f319ac8bdcf7053e071bce29e<br>
<br>
-----------------------------------<br>
> End: 2012-10-02 14:24:54.364000<br>
</pre>
<hr /></li></ul>

Win \o/