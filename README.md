# Key4HEP_first_experience

![Link to the pdf and some description](power_vs_logE_withLHCandCERN.pdf)
![This is a test](power_vs_logE_withLHCandCERN.png)


This is a diary of my first attempt to use Key4HEP.

## November 10, 2022

- "google" (actually using ecosia) key4hep, first hit yields https://key4hep.github.io/key4hep-doc/
-  log in to lxplus.cern.ch
- create new directory key4hep
- start to follow instructions on https://key4hep.github.io/key4hep-doc/ :
   - source /cvmfs/sw.hsf.org/key4hep/setup.sh 
       - yields: /cvmfs/sw.hsf.org/key4hep/setup.sh:export:4: not valid in this context: key4hep-stack/2022-10-30
       - probably need to use bash ? => yes, when using bash, the setup scripts seems to work
   - since I don't want to run the CLIC simulation & reconstruction, but a kinematic fit example, I clone 
       - the MarlinKinfit tutorial:  git clone https://github.com/ILDAnaSoft/MarlinKinfitTutorial.git
       - MarlinKinfitProcessors : git clone https://github.com/iLCSoft/MarlinKinfitProcessors.git
       - the tutorial has local processors to be compiled, maybe start with one of the normal example processors from MarlinKinfitProcessors, WW5CFit
   - get some data file. 
     - Quick: scp one from DESY ... immediately out of disk space 
          **addendum Nov 11:** asked André -> increase home and add workspace on
          https://resources.web.cern.ch/resources/Manage/AFS/Settings.aspx
          got access to /eos/experiment/clicdp/grid/
     - for now: try on DESY NAF...:
       - mkdir key4hep/first_trial
       - bash
       - source /cvmfs/sw.hsf.org/key4hep/setup.sh 
       - git clone https://github.com/iLCSoft/MarlinKinfitProcessors.git
       - mkdir run, cp WW5CFit.xml there, change input file to   /pnfs/desy.de/ilc/prod/ilc/mc-opt-3/ild/dst-merged/500-TDR_ws/4f_WW_hadronic/ILD_l5_o1_v02/v02-00-01/rv02-00-01.sv02-00-01.mILD_l5_o1_v02.E500-TDR_ws.I250006.P4f_ww_h.eL.pR.n001.d_dstm_10398_0.slcio
  - here we are - now back to CLIC reconstruction instructions... https://key4hep.github.io/key4hep-doc/k4marlinwrapper/doc/starterkit/k4MarlinWrapperCLIC/Readme.html#reconstruction
     - need appropriate DD4hepXMLFile.... check what's in $LCGEO/ILD/compact/ILD_l5_o1_v02 - many xml files ....let's take simply *ILD_l5_o1_v02.xml* and hope for the best...
     - first try Marlin directly:
        - Marlin WW5CFit.xml --InitDD4hep.DD4hepXMLFile=$LCGEO/ILD/compact/ILD_l5_o1_v02/ILD_l5_o1_v02.xml
        - loading libraries works... but awfully slow ... and crash.
        -  ok, my mistake: WW5CFit.xml is so old that it doesn't have DD4HEPInit yet... Adding it from MarlinKinfitTutorials/ZHAnalysis.html, and try again
        -  works, and library loading fast :) 
        -  **addendum Nov 11:** can simply delete line with the GEAR file from .xml
        -  and hey, I get a root file out !!! :)


## November 11, 2022

-  login in to NAF again, bash, source /cvmfs/sw.hsf.org/key4hep/setup.sh 
-  now try MarlinWrapper as in https://key4hep.github.io/key4hep-doc/k4marlinwrapper/doc/starterkit/k4MarlinWrapperCLIC/Readme.html#reconstruction-with-gaudi-through-k4marlinwrapper
   -  convertMarlinSteeringToGaudi.py WW5CFit.xml WW5CFit.py  
   -  results in Exception when getting trees: ParseError('XML declaration not well-formed: line 1, column 20')
   -  arggh. 
   -  OK, git clone https://github.com/iLCSoft/CLICPerformance to check for difference wrt CLIC steering file
   -  ah, found typo (encondin instead of encoding), fixed, now have a WW5CFit.py :)
   -  changes proposed to .py file in instruction seem to be fine already...try it
   -  k4run WW5CFit.py
   -  runs, and produces a root file -> check for differences...=> only very few events => ah, **MaxRecordNumber is not translated in xml->py conversion**
   -  set maxevts directly in .py from 10 to 1000 and try again... hm, it says it reads 1000 event and finds 4000 jets
   -  ... but actually histos still nearly empty... log file seems to indicate that histograms are re-creted for each event ?!
      **addendum Nov 11: RAIDA works, problem is related to private function isFirstEvent() of the Marlin base processor class, which cannot be called by wrapper. Would need to become public... SOLUTION: move histo booking to init()**
- what next? Try compiling local processor. Needed for: ZH example and switching on tracer, and for working on LCIO / EDM4HEP output
- https://key4hep.github.io/key4hep-doc/developing-key4hep-software/Key4hepCMakeGuide.html
   - mkdir build; cd build; cmake .. - all fine
   - make install is a desaster, thousands of: 
/cvmfs/sw.hsf.org/spackages6/root/6.26.06/x86_64-centos7-gcc11.2.0-opt/bc7bv/include/ROOT/RConfig.hxx:48:4: error: #error "ROOT requires support for C++14 or higher."
   48 | #  error "ROOT requires support for C++14 or higher."
      |    ^~~~~
/cvmfs/sw.hsf.org/spackages6/root/6.26.06/x86_64-centos7-gcc11.2.0-opt/bc7bv/include/ROOT/RConfig.hxx:50:5: error: #error "Pass `-std=c++14` as compiler argument."
   50 | #   error "Pass `-std=c++14` as compiler argument."
   
   **Solution: use cmake -D CMAKE_CXX_STANDARD=17 ..**
- ok, so now changed WW5CFit to book histos in initialize and successfully compiled, linked, installed. 
- CLIC instructions say that only LD_LIBRARYPATH needs updating, export LD_LIBRARY_PATH=$PWD/lib:$PWD/lib64:$LD_LIBRARY_PATH   
- **need also to update MARLIN_DLL as usual with local processors, this is missing in the CLIC instructions**
- runs, write root file, with histograms filled (yeah!) - but ends with seg fault :( 
- is this the "usual" one?! => Andre says no, something with processor parameters ?! to be continued next week...

## November 21:

- Julie's setup requires standard processors with configuration / weight files (IsoLep, LCFIPLus, ...) => where are these "extra files" stored in key4hep??? Frank: not at all, all of ILDConfig needs to be downloaded from github

## November 22:

- figured out how to change login shell on lxplus -> account.cern.ch and a lot of clicking .... :)
- 

