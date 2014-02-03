##The Problem
Users with test solutions that include many (or very large) assemblies sometimes report slow test runs with FakeItEasy. Often this manifests as a very long time taken for the initial test to complete.

##The Cause
This behaviour is usually caused by the way FakeItEasy looks for user-defined extension points. It loads and scans all DLLs in the working directory that aren't already in the AppDomain. If there are many such DLLs, this can take several seconds. The time is only spent on the first call to FakeItEasy, but it can be quite annoying.

##Possible Remedies
1. First, if you're **using a FakeItEasy older than 1.13.0**, upgrade now. 1.13.0 included [an improvement to the scanning procedure][2] that will speed things up in many cases.

2. If you're **already using 1.13.0 or newer**, and you still have the problem, check to see whether your test application is using [Shadow Copies][1]. The scanning improvements implemented in 1.13.0 are fooled by shadow copies, so non-AppDomain DLLs are still loaded.  
If shadow copies are enabled, and not needed, try disabling them. Often that will result in a large reduction in startup time. For both the [xUnit.net][4] and [NUnit][5] console runners, this is done with the `/noshadow` command line option.
3. If **shadow copies are off, or required**, then there is currently no ready-made solution for you, but [we're working on it][3].


[1]:http://msdn.microsoft.com/en-us/library/ms404279(v=vs.110).aspx
[2]:../issues/133
[3]:../issues/130
[4]:http//xunit.net
[5]:http://www.nunit.org/