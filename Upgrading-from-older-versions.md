Some useful regular expressions for upgrading code written for older versions of FakeItEasy:

Configure.Fake(...
Find:
	Configure[\.:b\n]*Fake\({[:Al:Nu:Pu]*}\)[\.:b\n]*CallsTo\([:Al] =\> [:Al]\.
Replace:
	A.CallTo(() => \1.
