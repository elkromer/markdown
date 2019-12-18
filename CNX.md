---
tags:
  - Cnx
---

Really just using templates to create wrappers and that's it.

!!! j2cs and j2cp are completely standalone translation scripts. !!!

MAKE DATA
bldmaps generates index stuff and in decendant folders -> otherwise entities in the help source have no definitions
nsgmls is the new sgml scanner. it goes through a parses all the help source for the product and generates a temp.out file that contains a line-oriented representation of properties and methods and configs.
	the very first character signifies what is on the line
	a is an attribute
	hyphen is regular ass text
	parens are start and end elements
genalrec_sqlite.tcl opens nsgmls output and parses line by line to spit out sqlite db
	fast. calls tcl methods based on nsgmls output
cnx.tcl/makefile_?prod=IPWorks&output regenerates the top level product makefile based on cnx templates.

MAKE URLS
at its core, make urls first uses cnx templates to create makefiles for all the editions a product has. then it uses the data in the sqlite database and more cnx templates to populate the files in the Release/prod folder
	note: if you make a structural change to the help source then you have to 
	regenerate the sqlite database and call make urls 
urls takes the templates and injects sqlite database information and the end result is the output files generated in release.
	public api or wrapper in source code form

Q&A
so what is the primary purpose of the make urls output files in the Release/prod folder?
	those are the wrappers - the public API. Since we have two different APIs (our internal api with calls that components make from component to component do_connect, do_logoff, etc. and our customer-facing API which goes by the documentation) the wrappers are definitions for the public API methods which call internal methods.
what do those makefiles do?
	there is no reason to ever call them directly. they only exist as a way to run simple make commands from the Release/prod makefile. THAT makefile is the controller. make data will create help definitions (index.* files), parse the help, add everything to sqlite database. make urls will take in cnx templates and use them in combination with the items in the sqlite database to generate the wrappers. that is all you will really ever need to do on your own machine. Those wrappers and the generated code from j2cs and j2cp are all that is needed to build the debug project.
How are the debug projects related to the files in the Release/prod folder?
	The debug projects use the code-generated code and wrappers to create a huge project you can step through at every level. If you make a local code change you shouldn't need to do anything to update the debug project because the files are linked. If you make a structural API change in the help then you need to run make data, make urls, j2cp, j2cs. (sometimes need to regenerate the debug project files but not usually).
what is the process for testing a code fix in a large product without rebuilding the entire thing?
	generally if you are building something to test a customer's fix then build it on j3. if you made a small code change in one product in one edition in IPWorks then copy the files to j3, `make data urls _cs` and after urls is done, the _cs target will make the wrappers, parse the help again (csjava stuff needs to happen again), and package the binaries and help into Publish.
what is the priv target used for?
	cdata. don't even worry about it.

