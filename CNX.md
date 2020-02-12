---
tags:
  - Cnx
---

# CNX
> A public API generation system based on custom templates. It really just uses templates to create wrappers and that's it.

## `make data`
- `bldmaps.tcl` generates the definitions for the entities in the help recursively (index.ref and index.map)
- Runs `nsgmls` (the new sgml scanner). It goes through and parses all the help source for a product and generates a temp.out file
	+ `temp.out` contains a line-oriented representation of properties and methods and configs.
		- the very first character signifies what is on the line
		- `a` is an attribute
		- `hyphen` is regular ass text
		- parenthesis are start and end elements
- `genalrec_sqlite.tcl` opens nsgmls output and parses line by line and adds records to the sqlite database
	+ it is very fast. calls tcl methods based on nsgmls output
- `cnx.tcl/makefile_?prod=IPWorks&output` regenerates the top level product makefile based on cnx templates.

## `make urls`
- make urls first uses cnx templates to create wrapper makefiles for all the editions a product has (in `v20/Release/prod`)
- then it uses the data in the sqlite database and more cnx templates to populate the files in the Release/prod folder (this is the public api or wrappers in source code form)
	+ if you make a structural change to the help source then you have to regenerate the sqlite database with `make data` and call `make urls` 
	+ urls takes the templates and injects sqlite database information and the end result is the output files generated in release.

## `make _target`
- Rebuilds the public api based on the local changes on disk. **You must SVN Update by hand.**
- The installer will be refreshed with the new library but other things like the build number won't be updated


## Notes

- `j2cs.tcl` and `j2cp.tcl` are completely standalone translation scripts.


## Q&A
- so what is the primary purpose of the make urls output files in the Release/prod folder?
> those are the wrappers - the public API. Since we have two different APIs (our internal api with calls that components make from component to component do_connect, do_logoff, etc. and our customer-facing API which goes by the documentation) the wrappers are definitions for the public API methods which call internal methods.
- what do those makefiles do?
> there is no reason to ever call them directly. they only exist as a way to run simple make commands from the Release/prod makefile. THAT makefile is the controller. `make data` will create help definitions, parse the help, add everything to sqlite database. `make urls` will take in cnx templates and use them in combination with the items in the sqlite database to generate the wrappers. that is all you will really ever need to do on your own machine. Those wrappers and the generated code from `j2cs` and `j2cp` are all that is needed to build the debug project.
- How are the debug projects related to the files in the Release/prod folder?
> The debug projects use the code-generated code and wrappers to create a huge project you can step through at every level. If you make a local code change you shouldn't need to do anything to update the debug project because the files are linked. If you make a structural API change in the help then you need to run `make data`, `make urls`, `j2cp`, `j2cs`. (sometimes need to regenerate the debug project files but not usually).
- what is the process for testing a code fix in a large product without rebuilding the entire thing?
> generally if you are building something to test a customer's fix then build it on j3. if you made a small code change in one product in one edition in IPWorks then copy the files to j3, `make data urls _cs` and after urls is done, the _cs target will make the wrappers, parse the help again (csjava stuff needs to happen again), and package the binaries and help into Publish.
- what is the priv target used for?
> cdata. don't even worry about it.


