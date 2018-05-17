### ipoke~, an interpolating buffer writer
by Pierre Alexandre Tremblay (2005-2018)
v.4 added overdub ratio and Max6 support
v.4.1 adds 64bit Max support - thanks to the FluCoMa project funded by the European Research Council (ERC) under the European Union’s Horizon 2020 research and innovation programme (grant agreement No 725899)
v.4.1.1 corrected the behaviour when starting to write after negative indices

#### Description
This is an interpolating version of the staple Max object poke~, which allows to write to the buffers without leaving unfilled indices when writing faster than real-time.

The maintenance, update, and 64 bit port were made possible thanks to the FluCoMa project (http://www.flucoma.org/) funded by the European Research Council (https://erc.europa.eu/) under the European Union’s Horizon 2020 research and innovation programme (grant agreement No 725899)

#### Why was this utility needed
It was impossible to emulate the musical behaviour of bespoke resampling digital delay pedals in Max. Now it is.

#### How to Install from binaries (Max 5 and up)
Download the package and read the text file.

#### How to build from the source code
On MacOS, the XCode project should build (tested on XCode 9.2)

On Windows:
  1. install the 'max-sdk-master' in your Max7 package folder. (tested with SDK dated 6 Dec 2017)
  2. put the 'ipoke~' folder in the 'max-sdk-master/source/audio'
  3. double-click on the project. It should compile (tested with VS 2017 v.141)

On either, do not forget to build for release, as optimisation is crucial.

#### Enjoy! Comments, suggestions and bug reports are welcome.
